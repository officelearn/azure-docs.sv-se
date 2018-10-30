---
title: 'Azure AD Connect: Väljer du installationstypen av | Microsoft Docs'
description: Den här artikeln vägleder dig genom hur du väljer installationstypen som du vill använda för Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: e4185b6b30bdd01492d9aae7ac38e18aaba7eef5
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312048"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Välj vilken installationstyp du vill använda för Azure AD Connect
Azure AD Connect har två typer av appinstallationer för nyinstallation: Express och anpassad. Det här avsnittet hjälper dig att avgöra vilket alternativ du ska använda under installationen.

## <a name="express"></a>Express
Express är det vanligaste alternativet och används av cirka 90% av alla nya installationer. Det har utformats för att tillhandahålla en konfiguration som passar för de vanligaste scenarierna för kunden.

Den förutsätter:

- Du har en enda Active Directory-skogen på plats.
- Du har ett enterprise-administratörskonto som du kan använda för installationen.
- Du har mindre än 100 000 objekt i din lokala Active Directory.

Du får:

- [Synkronisering av lösenordshash](how-to-connect-password-hash-synchronization.md) från lokalt till Azure AD för enkel inloggning.
- En konfiguration som synkroniserar [användare, grupper, kontakter och Windows 10-datorer](concept-azure-ad-connect-sync-default-configuration.md).
- Synkroniseringen av alla berättigade objekt i alla domäner och alla organisationsenheter.
- [Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) är aktiverad för att kontrollera att du alltid använder den senaste tillgängliga versionen.

Alternativ där du kan fortfarande använda Express:

- Om du inte vill synkronisera alla organisationsenheter du kan fortfarande använda Express och på den sista sidan avmarkera **starta synkroniseringsprocessen...**\*. Kör installationsguiden igen och ändra organisationsenheter i [konfigurationsalternativ](how-to-connect-installation-wizard.md#customize-synchronization-options) och aktivera schemalagd synkronisering.
- Du vill aktivera en av funktionerna i Azure AD Premium, till exempel tillbakaskrivning av lösenord. Först gå igenom express för att hämta den inledande installationen har slutförts. Kör installationsguiden igen och ändra den [konfigurationsalternativ](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Anpassat
Anpassad sökväg kan många fler alternativ än express. Det bör användas i samtliga fall där konfigurationen som beskrivs i föregående avsnitt för snabba inte är representativ för din organisation.

Använd när:

- Du har inte åtkomst till ett företagsadministratörskonto i Active Directory.
- Du har fler än en skog eller om du planerar att synkronisera fler än en skog i framtiden.
- Du har domäner i skogen kan inte nås från Connect-servern.
- Du planerar att använda federation eller direktautentisering för användarinloggning.
- Du har fler än 100 000 objekt och måste använda en fullständig SQL Server.
- Du planerar att använda gruppbaserad filtrering och inte bara domän eller OU-baserad filtrering.

## <a name="upgrade-from-dirsync"></a>Uppgradera från DirSync
Om du använder DirSync, följer du stegen i [uppgradera från DirSync](how-to-dirsync-upgrade-get-started.md) att uppgradera din befintliga konfiguration. Det finns två olika uppgraderingsalternativen:

- Uppgradering på plats om du vill installera Connect på samma server.
- Parallell distribution för att installera Connect på en ny server när den befintliga DirSync-servern är fortfarande fungerar.

## <a name="upgrade-from-azure-ad-sync"></a>Uppgradera från Azure AD-synkronisering
Om du använder Azure AD Sync så kan du följa den [likadant](how-to-upgrade-previous-version.md) som när du uppgraderar från en version av Anslut till en nyare. Det finns två olika uppgraderingsalternativen:

- Uppgradering på plats om du vill installera Connect på samma server.
- Swingmigrering installera Connect på en ny server när den befintliga Azure AD Sync-servern är fortfarande fungerar.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrera från FIM2010 eller MIM2016
Om du använder Forefront Identity Manager 2010 eller Microsoft Identity Manager 2016 med Azure AD Connector, är ditt enda alternativ en migrering. Följ stegen som beskrivs i [swingmigrering](how-to-upgrade-previous-version.md#swing-migration). Ersätt nämns Azure AD Sync med FIM2010/MIM2016 i steg.

## <a name="next-steps"></a>Nästa steg
Beroende på vilket alternativ som du har valt för att använda att använda innehållsförteckningen till vänster för att hitta din artikel med detaljerade anvisningar.
