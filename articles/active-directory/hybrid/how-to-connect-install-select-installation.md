---
title: 'Azure AD Connect: Välj din installationstyp | Microsoft-dokument'
description: I det här avsnittet får du lära dig hur du väljer den installationstyp som ska användas för Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90a624a6b3b4696899af0d8606f653df260cc201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60348288"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Välj vilken installationstyp du vill använda för Azure AD Connect
Azure AD Connect har två installationstyper för ny installation: Express och anpassad. Det här avsnittet hjälper dig att bestämma vilket alternativ som ska användas under installationen.

## <a name="express"></a>Express
Express är det vanligaste alternativet och används av cirka 90% av alla nya installationer. Den har utformats för att tillhandahålla en konfiguration som fungerar för de vanligaste kundscenarierna.

Det förutsätter:

- Du har en enda Active Directory-skog lokalt.
- Du har ett företagsadministratörskonto som du kan använda för installationen.
- Du har färre än 100 000 objekt i den lokala Active Directory.You have less than 100,000 objects in your local Active Directory.

Du får:

- [Synkronisering av lösenord hash-synkronisering](how-to-connect-password-hash-synchronization.md) från lokalt till Azure AD för enkel inloggning.
- En konfiguration som synkroniserar [datorer för användare, grupper, kontakter och Windows 10](concept-azure-ad-connect-sync-default-configuration.md).
- Synkronisering av alla kvalificerade objekt i alla domäner och alla ru:er.
- [Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) är aktiverad för att se till att du alltid använder den senaste tillgängliga versionen.

Alternativ där du fortfarande kan använda Express:

- Om du inte vill synkronisera alla ru: er kan du fortfarande använda Express och på sista sidan avmarkera **Starta synkroniseringsprocessen...***. Kör sedan installationsguiden igen och ändra de uppbest i [konfigurationsalternativen](how-to-connect-installation-wizard.md#customize-synchronization-options) och aktivera schemalagd synkronisering.
- Du vill aktivera en av funktionerna i Azure AD Premium, till exempel återställning av lösenord. Först gå igenom Express för att få den första installationen klar. Kör sedan installationsguiden igen och ändra [konfigurationsalternativen](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Anpassat
Den anpassade sökvägen tillåter många fler alternativ än express. Den bör användas i alla fall där konfigurationen som beskrivs i föregående avsnitt för express inte är representativ för din organisation.

Använd när:

- Du har inte åtkomst till ett företagsadministratörskonto i Active Directory.
- Du har mer än en skog eller planerar att synkronisera mer än en skog i framtiden.
- Du har domäner i skogen som inte kan nås från Connect-servern.
- Du planerar att använda federations- eller direktautentisering för användarloggning.
- Du har fler än 100 000 objekt och måste använda en fullständig SQL Server.
- Du planerar att använda gruppbaserad filtrering och inte bara domän- eller organisationsenhetsbaserad filtrering.

## <a name="upgrade-from-dirsync"></a>Uppgradera från DirSync
Om du för närvarande använder DirSync följer du stegen i [Uppgradera från DirSync](how-to-dirsync-upgrade-get-started.md) för att uppgradera din befintliga konfiguration. Det finns två olika uppgraderingsalternativ:

- Uppgradering på plats för att installera Connect på samma server.
- Parallell distribution för att installera Connect på en ny server medan den befintliga DirSync-servern fortfarande är i drift.

## <a name="upgrade-from-azure-ad-sync"></a>Uppgradera från Azure AD Sync
Om du för närvarande använder Azure AD Sync kan du följa [samma steg](how-to-upgrade-previous-version.md) som när du uppgraderar från en Connect-version till en nyare. Det finns två olika uppgraderingsalternativ:

- Uppgradering på plats för att installera Connect på samma server.
- Swing-migrering för att installera Connect på en ny server medan den befintliga Azure AD Sync-servern fortfarande är i drift.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrera från FIM2010 eller MIM2016
Om du för närvarande använder Forefront Identity Manager 2010 eller Microsoft Identity Manager 2016 med Azure AD Connector är ditt enda alternativ en migrering. Följ stegen som beskrivs i [swing-migration](how-to-upgrade-previous-version.md#swing-migration). Ersätt eventuell omnämnande av Azure AD Sync med FIM2010/MIM2016 i stegen.

## <a name="next-steps"></a>Nästa steg
Beroende på vilket alternativ du har valt att använda använder du innehållstabellen till vänster för att hitta artikeln med detaljerade steg.
