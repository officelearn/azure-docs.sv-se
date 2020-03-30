---
title: Tillgängliga anpassade administratörsrollbehörigheter – Azure AD | Microsoft-dokument
description: Anpassade administratörsrollbehörigheter för delegering av identitetshantering.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6156857202c1cca94df6d70ec2059daf55178f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025156"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Undertyper och behörigheter för programregistrering i Azure Active Directory

Den här artikeln innehåller de tillgängliga appregistreringsbehörigheterna för anpassade rolldefinitioner i Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-directory-applications"></a>Behörigheter för hantering av program med en katalog

När du väljer behörigheter för din anpassade roll har du möjlighet att bevilja åtkomst för att hantera endast enkatalogprogram. Enkatalogprogram är endast tillgängliga för användare i Azure AD-organisationen där programmet är registrerat. Enkatalogprogram definieras som att **kontotyper som stöds** har angetts till "Konton i den här organisationskatalogen endast". I Graph API har program med en katalog egenskapen signInAudience inställd på "AzureADMyOrg".

Om du vill bevilja åtkomst till att endast hantera enkatalogprogram använder du behörigheterna nedan med undertypen **applications.myOrganization**. Till exempel microsoft.directory/applications.myOrganization/basic/update.

Se [översikten över anpassade roller](roles-custom-overview.md) för en förklaring av vad de allmänna termerna undertyp, behörighet och egenskapsuppsättning betyder. Följande information är specifik för ansökan registreringar.

### <a name="create-and-delete"></a>Skapa och ta bort

Det finns två behörigheter för att bevilja möjligheten att skapa programregistreringar, var och en med olika beteende:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

Om du tilldelar den här behörigheten blir skaparen den första ägaren av den skapade appregistreringen, och den skapade appregistreringen räknas mot skaparens kvot med 250 skapade objekt.

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

Tilldelning av den här behörigheten resulterar i att skaparen inte läggs till som den första ägaren av den skapade appregistreringen, och den skapade appregistreringen räknas inte mot skaparens 250 skapade objektkvot. Använd den här behörigheten noggrant eftersom det inte finns något som hindrar förvärvaren från att skapa appregistreringar förrän kvoten på katalognivå har nåtts. Om båda behörigheterna har tilldelats har den här behörigheten företräde.

Om båda behörigheterna har tilldelats har behörigheten /create företräde. Även om behörigheten /createAsOwner inte automatiskt lägger till skaparen som den första ägaren, kan ägare anges när appregistreringen skapas när diagram-API:er eller PowerShell-cmdletar används.

Skapa behörigheter ger åtkomst till kommandot **Ny registrering.**

[Dessa behörigheter ger åtkomst till kommandot Ny registreringsportal](./media/roles-create-custom/new-custom-role.png)

Det finns två behörigheter för att bevilja möjligheten att ta bort appregistreringar:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/program/delete

Ger möjlighet att ta bort appregistreringar oavsett undertyp. det vill ha både program med en klient och flera innehavare.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directory/applications.myOrganisering/borttagning

Ger möjlighet att ta bort appregistreringar som är begränsade till dem som endast är tillgängliga för konton i organisationen eller program med en enda klient (undertypen minorganisation).

![Dessa behörigheter ger åtkomst till kommandot Ta bort appregistrering](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> När du tilldelar en roll som innehåller skapa behörigheter måste rolltilldelningen göras i katalogomfånget. En skapa-behörighet som tilldelats i ett resursomfattning ger inte möjlighet att skapa appregistreringar.

### <a name="read"></a>Läsa

Alla medlemsanvändare i organisationen kan läsa information om appregistrering som standard. Det går dock inte att gästanvändare och programtjänsthuvudnamn. Om du planerar att tilldela en roll till en gästanvändare eller gästprogram måste du inkludera lämpliga läsbehörigheter.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directory/applications/allProperties/read microsoft.directory/applications/allProperties/read microsoft.directory/applications/allProperties/read microsoft.

Möjlighet att läsa alla egenskaper för program med en klient och flera innehavare utanför egenskaper som inte kan läsas i alla situationer som autentiseringsuppgifter.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganization/allProperties/read

Ger samma behörigheter som microsoft.directory/applications/allProperties/read, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read microsoft.directory/applications/owners/read microsoft.directory/applications/owners/read microsoft.

Ger möjlighet att läsa egendom för ägare på program med en klient och flera innehavare. Ger åtkomst till alla fält på sidan för ansökans registreringsägare:

![Dessa behörigheter ger åtkomst till sidan för appregistreringsägare](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft.directory/applications/standard/read

Ger åtkomst till lässtandarda programregistreringsegenskaper. Detta inkluderar egenskaper över programregistreringssidor.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganisering/standard/läs

Ger samma behörigheter som microsoft.directory/applications/standard/read, men endast för program med en klient.

### <a name="update"></a>Uppdatering

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directory/applications/allProperties/update

Möjlighet att uppdatera alla egenskaper på program med en katalog och flera kataloger.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganization/allProperties/update

Ger samma behörigheter som microsoft.directory/applications/allProperties/update, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Möjlighet att uppdatera egenskapen account type (signInAudience) på program med en katalog och flera kataloger.

![Den här behörigheten ger åtkomst till kontotypsegenskap för appregistrering som stöds på autentiseringssidan](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganisering/publik/uppdatering

Ger samma behörigheter som microsoft.directory/applications/audience/update, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/program/autentisering/uppdatering

Möjlighet att uppdatera svars-URL:en, ut signerings-URL:en, implicit flöde och utgivardomänegenskaper för program med en enda klient och flera innehavare. Ger åtkomst till alla fält på autentiseringssidan för programregistrering utom kontotyper som stöds:

![Ger åtkomst till autentisering av appregistrering men stöds inte kontotyper](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganisering/autentisering/uppdatering

Ger samma behörigheter som microsoft.directory/applications/authentication/update, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

Möjlighet att uppdatera namn, logotyp, url till startsidan, villkor för tjänst-URL och webbadresser för sekretesspolicyn för program med en enda klient och flera innehavare. Ger åtkomst till alla fält på varumärkessidan för programregistrering:

![Den här behörigheten ger åtkomst till varumärkessidan för appregistrering](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganisering/basic/update

Ger samma behörigheter som microsoft.directory/applications/basic/update, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/program/autentiseringsuppgifter/uppdatering

Möjlighet att uppdatera egenskaperna för certifikat och klienthemligheter på program med en klient och flera innehavare. Ger åtkomst till alla fält på sidan registreringsbevis för program & hemligheter:

![Den här behörigheten ger åtkomst till sidan för appregistreringsbevis & hemligheter](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganisering/autentiseringsuppgifter/uppdatering

Ger samma behörigheter som microsoft.directory/applications/credentials/update, men endast för program med en katalog.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

Möjlighet att uppdatera ägaregenskapen på en klient och flera innehavare. Ger åtkomst till alla fält på sidan för ansökans registreringsägare:

![Dessa behörigheter ger åtkomst till sidan för appregistreringsägare](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganisering/ägare/uppdatering

Ger samma behörigheter som microsoft.directory/applications/owners/update, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/program/behörigheter/uppdatering

Möjlighet att uppdatera delegerade behörigheter, programbehörigheter, auktoriserade klientprogram, nödvändiga behörigheter och bevilja medgivandeegenskaper för program med en klient och flera innehavare. Ger inte möjlighet att utföra samtycke. Ger åtkomst till alla fält i API-behörigheterna för programregistrering och exponerar en API-sidor:

![De här behörigheterna ger åtkomst till sidan API-behörigheter för appregistrering](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![De här behörigheterna ger åtkomst till appregistreringen Exponera en API-sida](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganisering/behörigheter/uppdatering

Ger samma behörigheter som microsoft.directory/applications/permissions/update, men endast för program med en klient.

## <a name="required-license-plan"></a>Obligatorisk licensplan

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Nästa steg

- Skapa anpassade roller med [Azure-portalen, Azure AD PowerShell och Graph API](roles-create-custom.md)
- [Visa tilldelningar för en anpassad roll](roles-view-assignments.md)
