---
title: "Azure AD Connect: Välj installationstypen | Microsoft Docs"
description: "Det här avsnittet vägleder dig igenom hur du Välj installationstyp för Azure AD Connect"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 9b7bcd5856ec94367edd586c28c781ac9569a792
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Välj vilken installationstyp du använder för Azure AD Connect
Azure AD Connect har två typer av appinstallationer för nyinstallation: Express och anpassas. Det här avsnittet hjälper dig att avgöra vilket alternativ som ska användas under installationen.

## <a name="express"></a>Express
Express är det vanligaste alternativet och används av ungefär 90% av alla nya installationer. Det har utformats för att tillhandahålla en konfiguration som passar för de vanligaste scenarierna för kunden.

Vi utgår från:

- Du har en enda Active Directory-skogen lokalt.
- Du har ett enterprise-administratörskonto som du kan använda för att installera.
- Du har mindre än 100 000 objekt i din lokala Active Directory.

Du får:

- [Lösenordssynkronisering](active-directory-aadconnectsync-implement-password-synchronization.md) från lokal till Azure AD för enkel inloggning.
- En konfiguration som synkroniserar [användare, grupper, kontakter och Windows 10-datorer](active-directory-aadconnectsync-understanding-default-configuration.md).
- Synkroniseringen av alla tillgängliga objekt i alla domäner och alla organisationsenheter.
- [Automatisk uppgradering](active-directory-aadconnect-feature-automatic-upgrade.md) är aktiverad för att kontrollera att du alltid använder den senaste tillgängliga versionen.

Alternativ där du kan fortfarande använda Express:

- Om du inte vill synkronisera alla organisationsenheter du fortfarande kan använda snabb och på den sista sidan avmarkera ** starta synkroniseringsprocessen... ***. Kör installationsguiden igen och ändra organisationsenheter i [konfigurationsalternativ](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options) och aktivera schemalagd synkronisering.
- Du vill aktivera en av funktionerna i Azure AD Premium, till exempel tillbakaskrivning av lösenord. Först gå igenom express för att hämta den första installationen slutförts. Kör installationsguiden igen och ändra den [konfigurationsalternativ](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Anpassat
Den anpassade sökvägen tillåter många fler alternativ än express. Det ska användas i samtliga fall där konfigurationen som beskrivs i föregående avsnitt för snabba inte är representativa för din organisation.

Använd när:

- Du har inte åtkomst till ett företagsadministratörskonto i Active Directory.
- Du har mer än en skog eller om du planerar att synkronisera mer än en skog i framtiden.
- Du har domäner i skogen kan inte nås från Connect-servern.
- Du planerar att använda federation eller direktautentisering för användarinloggning.
- Du har mer än 100 000 objekt och måste använda en fullständig SQL Server.
- Du planerar att använda gruppbaserade filtrering och inte bara domän eller OU-baserade filtrering.

## <a name="upgrade-from-dirsync"></a>Uppgradera från DirSync
Om du använder DirSync, följer du stegen i [uppgradera från DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) att uppgradera den befintliga konfigurationen. Det finns två olika uppgraderingsalternativ:

- Uppgradering på plats om du vill installera Anslut på samma server.
- Parallell distribution för att installera Connect på en ny server när den befintliga DirSync-servern fungerar fortfarande.

## <a name="upgrade-from-azure-ad-sync"></a>Uppgradera från Azure AD Sync
Om du använder Azure AD Sync sedan kan du använda den [likadant](active-directory-aadconnect-upgrade-previous-version.md) som när du uppgraderar från en version av Anslut till en nyare. Det finns två olika uppgraderingsalternativ:

- Uppgradering på plats om du vill installera Anslut på samma server.
- Öppning migreringen att installera Connect på en ny server när den befintliga Azure AD Sync-servern är fortfarande fungerar.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrera från FIM2010 eller MIM2016
Om du använder Forefront Identity Manager 2010 eller Microsoft Identity Manager 2016 med Azure AD Connector, är det enda alternativet en migrering. Följ stegen som beskrivs i [öppning migrering](active-directory-aadconnect-upgrade-previous-version.md#swing-migration). Ersätt nämns Azure AD Sync med FIM2010/MIM2016 i steg.

## <a name="next-steps"></a>Nästa steg
Beroende på det alternativ som du har valt för att använda använder du tabell av innehåll till vänster för att hitta din artikeln med detaljerade anvisningar.
