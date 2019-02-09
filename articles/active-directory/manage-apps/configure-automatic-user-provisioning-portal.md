---
title: Hantering för företagsappar i Azure Active Directory för användaretablering | Microsoft Docs
description: Lär dig att hantera användarens kontoetablering för enterprise-appar som använder Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2018
ms.author: celested
ms.reviewer: asmalser
ms.openlocfilehash: ae4f92d9dc1671ab32a2a309da5095d59b9b5212
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961227"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Hantera konto etablering för företagsappar i Azure portal
Den här artikeln beskriver hur du använder den [Azure-portalen](https://portal.azure.com) att hantera etablering av automatisk användarkonto och avetablering för program som stöder den. Läs mer om automatisk användarkontoetablering och hur det fungerar i [automatisera Användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Hitta dina appar i portalen
Alla program som har konfigurerats för enkel inloggning i en katalog kan visas och hanteras i den [Azure-portalen](https://portal.azure.com). Programmen finns i den **alla tjänster** &gt; **företagsprogram** i portalen. Enterprise-appar är appar som distribueras och används i din organisation.

![Fönstret för företagsprogram](./media/configure-automatic-user-provisioning-portal/enterprise-apps-pane.png)

Att välja den **alla program** länken till vänster visar en lista över alla appar som har konfigurerats, inklusive appar som har lagts till från galleriet. Att välja en app läser in fönstret resurs för appen, där rapporter kan visas för appen och en mängd olika inställningar kan hanteras.

Användarkonto etableringsinställningar kan hanteras genom att välja **etablering** till vänster.

![Programmet resource fönstret](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Etablering lägen
Den **etablering** fönstret börjar med en **läge** meny som visar vilka etablering lägen stöds för ett företagsprogram och gör att de kan konfigureras. De tillgängliga alternativen är:

* **Automatisk** – det här alternativet visas om Azure AD stöder automatisk API-baserad etablering och/eller inaktivering av användarkonton till det här programmet. Om du väljer det här läget visas ett gränssnitt som hjälper administratörer via konfigurerar Azure AD för att ansluta till programmets API för användarhantering, skapa mappningar och arbetsflöden som definierar hur användare kontodata ska flöda mellan Azure AD och appen och hantera den Azure AD-etableringstjänsten.
* **Manuell** – det här alternativet visas om Azure AD inte stöder automatisk etablering av användarkonton till det här programmet. Det här alternativet innebär att användarkonton som lagras i programmet måste hanteras med hjälp av en extern process, baserat på användare hantering och etablering funktioner som tillhandahålls av programmet (som kan innehålla SAML just etablering).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens kontoetablering
Att välja den **automatisk** alternativet visar en skärm som är uppdelad i fyra delar:

### <a name="admin-credentials"></a>Autentiseringsuppgifter för administratör
Det här avsnittet är där autentiseringsuppgifterna som krävs för Azure AD för att ansluta till programmets Användarhantering API har angetts. Indata som krävs varierar beroende på programmet. Läs om autentiseringstyper och krav för specifika program i den [självstudierna för konfiguration för det specifika programmet](user-provisioning.md).

Att välja den **Testanslutning** knappen kan du testa autentiseringsuppgifterna genom att låta Azure AD-försök att ansluta till appen etablering app med hjälp av de angivna autentiseringsuppgifterna.

### <a name="mappings"></a>Mappningar
Det här avsnittet är där administratörer kan visa och redigera vilka attribut användarflödet mellan Azure AD och målprogrammet när användarkonton etableras eller uppdateras.

Det finns en förkonfigurerad uppsättning mappningar mellan Azure AD-användarobjekt och objekt för varje SaaS-app. Vissa appar hantera andra typer av objekt, till exempel grupper eller kontakter. Att välja en av dessa mappningar i tabellen visar redigeraren mappning till höger, där de kan visas och anpassade.

![Programmet resource fönstret](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Anpassningar som stöds är:

* Aktivera och inaktivera mappningar för specifika objekt, till exempel Azure AD-användarobjektet med SaaS-appens användarobjektet.
* Redigera attribut som flödar från Azure AD-användarobjektet till appens användarobjektet. Läs mer på attributmappning [förstå mappning attributtyper](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrera de etablering åtgärder som Azure AD utför på det aktuella programmet. I stället för att låta Azure AD fullt Synkronisera objekt, kan du begränsa de åtgärder som utförs. Till exempel genom att välja endast **uppdatering**, Azure AD endast säkerhetsuppdateringar befintliga konton i ett program och inte skapa nya. Genom att välja endast **skapa**, Azure endast skapar nya användarkonton men inte uppdatera befintliga. Den här funktionen gör att administratörer kan skapa olika mappningar för skapande av konto och uppdatera arbetsflöden.

### <a name="settings"></a>Inställningar
Det här avsnittet gör att administratörer kan starta och stoppa den Azure AD-etableringstjänsten för det valda programmet som du kan också rensa cacheminnet för etablering och starta om tjänsten.

Etablering har aktiverats för första gången för ett program, aktivera tjänsten genom att ändra den **Etableringsstatus** till **på**. Den här ändringen gör Azure AD provisioning-tjänst att utföra en inledande synkronisering, där det står att de användare som tilldelats i den **användare och grupper** avsnittet frågar målprogrammet för dem och sedan utför åtgärder för etablering definierats i Azure AD **mappningar** avsnittet. Under den här processen lagrar etableringstjänsten cachelagrade data om vilka användarkonton som hanteras så att icke-hanterade konton i målprogram som har aldrig inom omfånget för tilldelning inte påverkas av avetableringsförbättringar åtgärder. Efter den inledande synkroniseringen synkroniserar etableringstjänsten automatiskt användarobjektet och gruppobjektet med tio minuters intervall.

Ändra den **Etableringsstatus** till **av** bara pausar etableringstjänsten. I det här tillståndet Azure inte skapa, uppdatera eller ta bort användare eller gruppobjekt i appen. Ändra tillståndet till på gör att tjänsten ska ta vid där den slutade.

Att välja den **Avmarkera aktuell status och starta om synkronisering** kryssrutan och spara stoppar tjänsten etablering Dumpar cachelagrade data om vilken Azure AD-konton som hanterar, startar om tjänsterna och utför första synkroniseringen igen. Det här alternativet gör att administratörer kan distributionen etableringsprocessen börja om från början igen.

### <a name="synchronization-details"></a>Synkroniseringsinformation
Det här avsnittet innehåller ytterligare information om driften av etableringstjänsten, inklusive de första och sista etableringstjänsten körde programmet och hur många användare och gruppobjekt hanteras.

Länkar till den **etablering aktivitetsrapporten** som ger en logg över alla användare och grupper som skapas, uppdateras och borttagna mellan Azure AD och målprogrammet och den **etablering felrapport** som ger mer detaljerade felmeddelanden för användare och grupper objekt som inte gick att läsa, skapas, uppdateras eller tas bort. 



