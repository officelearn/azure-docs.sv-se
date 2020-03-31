---
title: 'Azure AD Connect: Nästa steg och hur du hanterar Azure AD Connect | Microsoft-dokument'
description: Lär dig hur du utökar standardkonfigurationen och driftuppgifterna för Azure AD Connect.
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
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c204029557a73dc3f02015afb92c0fdbf0d4d50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261299"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Nästa steg och hur du hanterar Azure AD Connect
Använd driftprocedurerna i den här artikeln för att anpassa Azure Active Directory (Azure AD) Connect för att uppfylla organisationens behov och krav.  

## <a name="add-additional-sync-admins"></a>Lägga till ytterligare synkroniseringsadministratörer
Som standard kan endast den användare som gjorde installationen och lokala administratörer hantera den installerade synkroniseringsmotorn. Om du vill att ytterligare personer ska kunna komma åt och hantera synkroniseringsmotorn letar du reda på gruppen ADSyncAdmins på den lokala servern och lägger till dem i den här gruppen.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Tilldela licenser till Azure AD Premium- och Enterprise Mobility Suite-användare
Nu när användarna har synkroniserats med molnet måste du tilldela dem en licens så att de kan komma igång med molnappar som Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Så här tilldelar du en Azure AD Premium- eller Enterprise Mobility Suite-licens

1. Logga in på Azure-portalen som administratör.
2. Välj **Active Directory**till vänster .
3. Dubbelklicka på den katalog som har de användare som du vill konfigurera på sidan **Active Directory.**
4. Överst på katalogsidan väljer du **Licenser**.
5. På sidan **Licenser** väljer du **Active Directory Premium** eller Enterprise Mobility **Suite**och klickar sedan på **Tilldela**.
6. I dialogrutan väljer du de användare som du vill tilldela licenser till och klickar sedan på bockmarkeringen för att spara ändringarna.

## <a name="verify-the-scheduled-synchronization-task"></a>Verifiera den schemalagda synkroniseringsaktiviteten
Använd Azure-portalen för att kontrollera status för en synkronisering.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Så här verifierar du den schemalagda synkroniseringsaktiviteten
1. Logga in på Azure-portalen som administratör.
2. Välj **Active Directory**till vänster .
3. Till vänster väljer du **Azure AD Connect**
4. Notera den senaste synkroniseringen högst upp på sidan.

![Tid för katalogsynkronisering](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Starta en schemalagd synkroniseringsaktivitet
Om du behöver köra en synkroniseringsuppgift kan du göra detta genom att:

1. Dubbelklicka på Azure AD Connect-genvägen för att starta guiden.
2. Klicka på **Konfigurera**.
3. På aktivitetsskärmen väljer du **alternativ för Anpassa synkronisering** och klickar på **Nästa**
4. Ange dina autentiseringsuppgifter för Azure AD
5. Klicka på **Nästa**. Klicka på **Nästa**.  Klicka på **Nästa**.
5.  Kontrollera att rutan **Starta synkroniseringsprocessen när konfigurationen** är klar är markerad på skärmen **Klar att konfigurera.**
6.  Klicka på **Konfigurera**.

Mer information om Azure AD Connect-synkroniseringsschemaläggaren finns i [Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Ytterligare uppgifter som är tillgängliga i Azure AD Connect
Efter den första installationen av Azure AD Connect kan du alltid starta guiden igen från startsidan för Azure AD Connect eller genvägen till skrivbordet.  Du kommer att märka att gå igenom guiden igen ger några nya alternativ i form av ytterligare uppgifter.  

I följande tabell finns en sammanfattning av dessa aktiviteter och en kort beskrivning av varje aktivitet.

![Lista över ytterligare uppgifter](./media/how-to-connect-post-installation/addtasks2.png)

| Ytterligare uppgift | Beskrivning |
| --- | --- |
|**Sekretessinställningar**|Visa vilka telemetridata som delas med Microsoft.|
|**Visa aktuell konfiguration**|Visa din aktuella Azure AD Connect-lösning.  Detta inkluderar allmänna inställningar, synkroniserade kataloger och synkroniseringsinställningar. |
| **Anpassa synkroniseringsalternativ** |Ändra den aktuella konfigurationen till exempel lägga till ytterligare Active Directory-skogar i konfigurationen eller aktivera synkroniseringsalternativ som användare, grupp, enhet eller lösenordsavskrivning. |
|**Konfigurera enhetsalternativ**|Enhetsalternativ som är tillgängliga för synkronisering|
|**Schema för uppdatera katalog**|Gör att du kan lägga till nya lokala katalogobjekt för synkronisering|
|**Konfigurera mellanlagringsläge** |Sceninformation som inte synkroniseras omedelbart och som inte exporteras till Azure AD eller lokal Active Directory.  Med den här funktionen kan du förhandsgranska synkroniseringarna innan de inträffar. |
|**Ändra inloggning för användare**|Ändra autentiseringsmetoden som användarna använder för att logga in|
|**Hantera federation**|Hantera DIN AD FS-infrastruktur, förnya certifikat och lägga till AD FS-servrar|
|**Felsöka**|Hjälp med felsökning av Azure AD Connect-problem|

## <a name="next-steps"></a>Nästa steg
Läs mer om [hur du integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
