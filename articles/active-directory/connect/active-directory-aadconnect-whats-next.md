---
title: "Azure AD Connect: Nästa steg och hur du hanterar Azure AD Connect | Microsoft Docs"
description: "Lär dig hur du utökar standardkonfigurationen och operativa uppgifter för Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: beace24fa00c85a5038a3c39ae8f76af5fd12111
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Nästa steg och hur du hanterar Azure AD Connect
Använd operativa procedurer i den här artikeln för att anpassa Azure Active Directory (AD Azure) Anslut för att uppfylla organisationens behov och krav.  

## <a name="add-additional-sync-admins"></a>Lägg till ytterligare sync-administratörer
Som standard bara användaren som gjorde installationen och lokala administratörer kan hantera installerade Synkroniseringsmotorn. För ytterligare användare kan komma åt och hantera Synkroniseringsmotorn, leta upp gruppen ADSyncAdmins på den lokala servern och lägga till dem i den här gruppen.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Tilldela licenser till användare i Azure AD Premium och Enterprise Mobility Suite
Nu när användarna har synkroniserats till molnet, måste du tilldela dem en licens så att de kan komma igång med molnappar som Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Så här tilldelar du en Azure AD Premium eller Enterprise Mobility Suite-licens

1. Logga in på Azure-portalen som en administratör.
2. Välj **Active Directory** till vänster.
3. På den **Active Directory** sidan genom att dubbelklicka på katalogen som har användare som du vill konfigurera.
4. Överst på katalogsidan väljer du **Licenser**.
5. På den **licenser** väljer **Active Directory Premium** eller **Enterprise Mobility Suite**, och klicka sedan på **tilldela**.
6. I dialogrutan väljer du de användare som du vill tilldela licenser till och klickar sedan på bockmarkeringen för att spara ändringarna.

## <a name="verify-the-scheduled-synchronization-task"></a>Kontrollera aktiviteten schemalagd synkronisering
Använda Azure portal för att kontrollera status för en synkronisering.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Att verifiera aktiviteten schemalagd synkronisering
1. Logga in på Azure-portalen som en administratör.
2. Välj **Active Directory** till vänster.
3. På den **Active Directory** sidan genom att dubbelklicka på katalogen som har användare som du vill konfigurera.
4. Markera överst på sidan directory **katalogintegrering**.
5. Under **integrering med lokala active directory**, Observera tid för senaste synkronisering.

<center>![Tid för Directory-synkronisering](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="start-a-scheduled-synchronization-task"></a>Starta en schemalagd synkronisering-aktivitet
Om du behöver köra en aktivitet för synkronisering kan du göra detta genom att köra Azure AD Connect-guiden igen.  Du måste ange dina autentiseringsuppgifter för Azure AD.  I guiden väljer du den **anpassa synkroniseringsalternativ** och på **nästa** att flytta via guiden. Se till att i slutet av **starta synkroniseringsprocessen så snart som den initiala konfigurationen är klar** är markerad.

<center>![Starta synkroniseringen](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Mer information om Azure AD Connect sync Scheduler finns [Azure AD Connect Scheduler](active-directory-aadconnectsync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Ytterligare uppgifter som är tillgängliga i Azure AD Connect
Efter den inledande installationen av Azure AD Connect, kan du alltid starta guiden igen från Azure AD Connect start sidan eller skrivbord genväg.  Du kommer att märka att gå igenom guiden igen ger nya alternativ i form av ytterligare uppgifter.  

Följande tabell innehåller en sammanfattning av dessa uppgifter och en kort beskrivning av varje aktivitet.

![Lista över ytterligare aktiviteter](./media/active-directory-aadconnect-whats-next/addtasks.png)

| Ytterligare uppgifter | Beskrivning |
| --- | --- |
| **Visa det valda scenariot** |Visa din aktuella Azure AD Connect-lösning.  Detta inkluderar allmänna inställningar synkroniseras kataloger och synkroniseringsinställningar. |
| **Anpassa synkroniseringsalternativ** |Ändra den aktuella konfigurationen som att lägga till ytterligare Active Directory-skogar konfigurationen eller aktivera synkroniseringsalternativ, till exempel användare, grupp, enhet eller tillbakaskrivning av lösenord. |
| **Aktivera Mellanlagringsläge** |Steg-information som synkroniseras inte omedelbart och kan inte exporteras till Azure AD eller lokala Active Directory.  Med den här funktionen kan du förhandsgranska synkroniseringar innan de inträffar. |

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
