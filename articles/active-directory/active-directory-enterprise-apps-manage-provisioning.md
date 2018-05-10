---
title: Hantering för företagsappar i Azure Active Directory för användaretablering | Microsoft Docs
description: Lär dig att hantera användare konto-etablering för företagsappar som använder Azure Active Directory
services: active-directory
documentationcenter: ''
author: asmalser
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: b57c6841566f3fd75ca0c48e055f54513247cb30
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Hantera konto-etablering för företagsappar i Azure-portalen
Den här artikeln beskriver hur du använder den [Azure-portalen](https://portal.azure.com) att hantera automatisk användar konto-etablering och avetablering för program som stöder detta, särskilt de som har lagts till i kategorin ”aktuell” för den [Azure Active Directory-programgalleriet](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Läs mer om automatisk konto användaretablering och hur det fungerar i [automatisera Användaretablering och avetablering för SaaS-program med Azure Active Directory](active-directory-saas-app-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Hitta dina appar i portalen
Alla program som är konfigurerade för enkel inloggning i en katalog med en directory-administratör med hjälp av den [Azure Active Directory-programgalleriet](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery), kan visas och hanteras i den [Azure-portalen](https://portal.azure.com). Programmen kan hittas i den **alla tjänster** &gt; **företagsprogram** avsnitt i portalen. Enterprise-appar är appar som har distribuerats och används i din organisation.

![Fönstret företagsprogram][0]

Att välja den **alla program** länken till vänster som visar en lista över alla appar som har konfigurerats, inklusive appar som har lagts till från galleriet. Markera en app laddas fönstret resurs för appen, där rapporter kan visas för appen och en rad olika inställningar som kan hanteras.

Användarkontot provisioning-inställningar kan hanteras genom att välja **etablering** till vänster.

![Programmet resurs fönstret][1]

## <a name="provisioning-modes"></a>Etablering lägen
Den **etablering** rutan börjar med en **läge** menyn som visar vilka etablering lägen stöds för ett företagsprogram och gör att de kan konfigureras. Tillgängliga alternativ inkluderar:

* **Automatisk** -det här alternativet visas om Azure AD stöder automatisk API-baserad etablering och/eller ta bort etableringen av användarkonton till det här programmet. Om du väljer det här läget visas ett gränssnitt som hjälper administratörer via konfigurerar Azure AD för att ansluta till programmets Användarhantering API, skapa mappningar av åtgärdskonto och arbetsflöden som definierar hur användarkonton ska flöda mellan Azure AD och appen och hantera Azure AD etableras.
* **Manuell** -det här alternativet visas om Azure AD inte stöder automatisk etablering av användarkonton till det här programmet. Det här alternativet innebär att kontot användarposter lagras i programmet måste hanteras med hjälp av en extern process, baserat på användare hantering och etablering funktioner som tillhandahålls av programmet (som kan omfatta etablering av SAML-in).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens konto-etablering
Att välja den **automatisk** visas en skärm som är indelat i fyra avsnitt:

### <a name="admin-credentials"></a>Autentiseringsuppgifter för administratör
Det här avsnittet är där autentiseringsuppgifterna som krävs för Azure AD för att ansluta till programmets Användarhantering API har angetts. Indata som krävs varierar beroende på programmet. Läs om autentiseringstyper och krav för specifika program i den [configuration självstudierna för det specifika programmet](active-directory-saas-app-provisioning.md).

Att välja den **Testa anslutning** knappen kan du testa autentiseringsuppgifter genom att låta Azure AD-försök att ansluta till appen etablering app med de angivna autentiseringsuppgifterna.

### <a name="mappings"></a>Mappningar
Det här avsnittet är där administratörer kan visa och redigera vad användaren attribut flödet mellan Azure AD och målprogrammet när användarkonton etablerades eller uppdaterades.

Det finns en förkonfigurerad uppsättning mappningar mellan användarobjekt i Azure AD och användarobjekt för varje SaaS-app. Vissa appar hantera andra typer av objekt, till exempel grupper eller kontakter. Att välja ett av dessa mappningar i tabellen visar redigeraren mappning till höger, där de kan visas och anpassat.

![Programmet resurs fönstret][2]

Anpassningar som stöds är:

* Aktivera och inaktivera mappningar för specifika objekt, till exempel Azure AD-användarobjekt som appen SaaS användarobjektet.
* Redigera de attribut som flödar från Azure AD-användarobjektet till appens användarobjektet. Mer information om attributmappning finns [förstå mappning attributtyper](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).
* Filtrera de etablering åtgärder som Azure AD utförs på det aktuella programmet. Du kan begränsa de åtgärder som utförs i stället för att Azure AD fullt Synkronisera objekt. Till exempel genom att bara välja **uppdatering**, Azure AD endast uppdateringar befintliga konton i ett program och inte skapa nya. Genom att bara välja **skapa**, Azure endast skapar nya användarkonton men inte uppdatera befintliga. Den här funktionen kan administratörer skapa olika mappningar för kontoskapande av och uppdatera arbetsflöden.

### <a name="settings"></a>Inställningar
Det här avsnittet tillåter administratörer att starta och stoppa Azure AD etableras för det valda programmet som du kan också etablering har rensat och starta om tjänsten.

Om etablering har aktiverats för första gången för ett program, aktivera tjänsten genom att ändra den **Status för etablering** till **på**. Den här ändringen gör att Azure AD etableras att utföra en inledande synkronisering där det står att användare som är tilldelade i den **användare och grupper** avsnittet frågar målprogrammet för dem och sedan utför åtgärder för etablering definieras i Azure AD **mappningar** avsnitt. Under den här processen lagrar tjänsten etablering cachelagrade data om vilka användarkonton som hanteras så att icke-hanterade konton i målprogram som fanns inte i omfånget för tilldelning inte påverkas av avetablering åtgärder. När den första synkroniseringen synkroniseras etablering tjänsten automatiskt användar- och gruppobjekt tio minuters intervall.

Ändra den **Status för etablering** till **av** bara pausar tjänsten etablering. I det här tillståndet Azure inte skapa, uppdatera eller ta bort användare eller gruppobjekt i appen. Ändra tillståndet till på gör att tjänsten ska ta vid där den avbröts.

Att välja den **Rensa aktuell status och starta om synkroniseringen** kryssrutan och spara stoppar tjänsten etablering Dumpar cachelagrade data om vilka Azure AD-konton som hanterar, startar om tjänsterna och utför den inledande synkroniseringen igen. Det här alternativet kan administratörer att starta etableringsprocessen distribution över igen.

### <a name="synchronization-details"></a>Synkroniseringsinformation
Det här avsnittet innehåller ytterligare information om driften av tjänsten etablering, inklusive de första och sista etablering tjänsten har körts för programmet och hur många användare och gruppobjekt som hanteras.

Länkar som den **etablering aktivitetsrapport** som ger en logg över alla användare och grupper som skapas, uppdateras och tas bort mellan Azure AD och målprogrammet, och den **etablering felrapporten** som ger mer detaljerade felmeddelanden för användare och grupp objekt som misslyckades med att läsa, skapa, uppdateras eller tas bort. 

## <a name="feedback"></a>Feedback

Skriv ned feedback kommer! Publicera din feedback och förslag på förbättringar i den **administrationsportalen** avsnitt i vår [Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Teknikteamet taggade om hur du skapar nya nya produkter varje dag och de använder din information i form och definierar vad du ska bygga härnäst.


[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-pane.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
