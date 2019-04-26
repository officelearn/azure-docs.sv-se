---
title: 'Azure AD Connect: Nästa steg och hur du hanterar Azure AD Connect | Microsoft Docs'
description: Lär dig hur du utökar standardkonfigurationen och operativa uppgifter för Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 291b3d506993cfea89be072684835c0d4efe75f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60243085"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Nästa steg och hur du hanterar Azure AD Connect
Använd operativa procedurer i den här artikeln för att anpassa Azure Active Directory (Azure AD) Connect för att uppfylla organisationens behov och krav.  

## <a name="add-additional-sync-admins"></a>Lägga till ytterligare synkronisering administratörer
Som standard bara den användare som utförde installation och lokala administratörer kan hantera installerade Synkroniseringsmotorn. För andra personer ska kunna komma åt och hantera Synkroniseringsmotorn, leta upp gruppen ADSyncAdmins på den lokala servern och lägga till dem i den här gruppen.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Tilldela licenser till användare för Azure AD Premium och Enterprise Mobility Suite
Nu när användarna har synkroniserats till molnet måste du tilldela dem en licens så att de kan komma igång med appar som Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Tilldela en Azure AD Premium eller Enterprise Mobility Suite-licens

1. Logga in på Azure portal som administratör.
2. Välj **Active Directory** till vänster.
3. På den **Active Directory** sidan, dubbelklicka på den katalog som innehåller de användare som du vill konfigurera.
4. Överst på katalogsidan väljer du **Licenser**.
5. På den **licenser** väljer **Active Directory Premium** eller **Enterprise Mobility Suite**, och klicka sedan på **tilldela**.
6. I dialogrutan väljer du de användare som du vill tilldela licenser till och klickar sedan på bockmarkeringen för att spara ändringarna.

## <a name="verify-the-scheduled-synchronization-task"></a>Verifiera uppgiften schemalagd synkronisering
Använd Azure-portalen för att kontrollera status för en synkronisering.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Att verifiera uppgiften schemalagd synkronisering
1. Logga in på Azure portal som administratör.
2. Välj **Active Directory** till vänster.
3. På den **Active Directory** sidan, dubbelklicka på den katalog som innehåller de användare som du vill konfigurera.
4. Överst på katalogsidan väljer **katalogintegrering**.
5. Under **integrering med lokala active directory**, Observera senaste synkronisering.

<center>

![Directory-synkroniseringstid](./media/how-to-connect-post-installation/verify.png)</center>

## <a name="start-a-scheduled-synchronization-task"></a>Starta en schemalagd synkronisering-aktivitet
Om du vill köra en aktivitet för synkronisering kan göra du detta genom att köra via Azure AD Connect-guiden igen.  Du måste ange dina autentiseringsuppgifter för Azure AD.  I guiden väljer du den **anpassa synkroniseringsalternativ** och på **nästa** att flytta via guiden. I slutet, kontrollerar du att den **starta synkroniseringsprocessen så snart som den första konfigurationen är klar** är markerad.

<center>

![Starta synkroniseringen](./media/how-to-connect-post-installation/startsynch.png)</center>

Läs mer på Azure AD Connect sync Scheduler [Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Ytterligare uppgifter som är tillgängliga i Azure AD Connect
Efter den inledande installationen av Azure AD Connect, kan du alltid starta guiden igen från början Azure AD Connect genvägen sida eller desktop.  Du kommer att märka att gå igenom guiden igen tillhandahåller vissa nya alternativ i form av ytterligare uppgifter.  

Följande tabell innehåller en sammanfattning av dessa uppgifter och en kort beskrivning av varje aktivitet.

![Lista över ytterligare uppgifter](./media/how-to-connect-post-installation/addtasks.png)

| Ytterligare uppgift | Beskrivning |
| --- | --- |
| **Visa det valda scenariot** |Visa din aktuella Azure AD Connect-lösning.  Detta inkluderar allmänna inställningar, synkroniserade kataloger och synkroniseringsinställningar. |
| **Anpassa synkroniseringsalternativ** |Ändra den aktuella konfigurationen som att lägga till ytterligare Active Directory-skogar konfigurationen eller aktivera synkroniseringsalternativ som användare, grupp, enhet eller tillbakaskrivningen av lösenord. |
| **Aktivera Mellanlagringsläge** |Steg-information som synkroniseras inte omedelbart och kan inte exporteras till Azure AD eller lokala Active Directory.  Med den här funktionen kan du förhandsgranska synkroniseringar innan de inträffar. |

## <a name="next-steps"></a>Nästa steg
Läs mer om [integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
