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
ms.date: 04/01/2019
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: d03ca64f3f3d2f034433f2aaa49f6babb7f9e5b4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59260297"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Hantera konto etablering för företagsappar i Azure portal

Den här artikeln beskriver hur du använder den [Azure-portalen](https://portal.azure.com) att hantera etablering av automatisk användarkonto och avetablering för program som stöder den. Läs mer om automatisk användarkontoetablering och hur det fungerar i [automatisera Användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Hitta dina appar i portalen

Använda Azure Active Directory-portalen för att visa och hantera alla program som har konfigurerats för enkel inloggning i en katalog. Enterprise-appar är appar som distribueras och används i din organisation. Följ stegen nedan för att visa och hantera dina företagsprogram:

1. Öppna den [Azure Active Directory-portalen](https://aad.portal.azure.com).

1. Välj **företagsprogram** i den vänstra rutan. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.

1. Välj en app för att läsa in dess resource fönstret där du kan visa rapporter och hantera appinställningar.

1. Välj **etablering** att hantera användarkonto etableringsinställningar för den valda appen.

   ![Programmet resource fönstret](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Etablering lägen

Den **etablering** fönstret börjar med en **läge** meny som visar etablering lägen som stöds för ett företagsprogram och låter dig konfigurera dem. De tillgängliga alternativen är:

* **Automatisk** – det här alternativet visas om Azure AD stöder automatisk API-baserad etablering eller inaktivering av användarkonton till det här programmet. Välj det här läget för att visa ett gränssnitt som hjälper administratörer att:

  * Konfigurera Azure AD för att ansluta till programmets API för användarhantering
  * Skapa mappningar och arbetsflöden som definierar hur användare kontodata ska flöda mellan Azure AD och appen
  * Hantera Azure AD provisioning-tjänst

* **Manuell** – det här alternativet visas om Azure AD inte stöder automatisk etablering av användarkonton till det här programmet. I detta fall använder måste användarkontot poster som lagras i programmet hanteras med hjälp av en extern process, baserat på användare hantering och etablering funktioner som tillhandahålls av programmet (som kan innehålla SAML just etablering).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens kontoetablering

Välj den **automatisk** alternativet för att ange inställningar för autentiseringsuppgifter som administratör, mappningar, starta och stoppa, och synkronisering.

### <a name="admin-credentials"></a>Autentiseringsuppgifter för administratör

Expandera **administratörsautentiseringsuppgifter** att ange de autentiseringsuppgifter som krävs för Azure AD för att ansluta till programmets API för användarhantering. Indata som krävs varierar beroende på programmet. Läs om autentiseringstyper och krav för specifika program i den [självstudierna för konfiguration för det specifika programmet](user-provisioning.md).

Välj **Testanslutning** för att testa autentiseringsuppgifterna genom att låta Azure AD-försök att ansluta till appen etablering app med hjälp av de angivna autentiseringsuppgifterna.

### <a name="mappings"></a>Mappningar

Expandera **mappningar** att visa och redigera användarattribut som flödar mellan Azure AD och målprogrammet när användarkonton etableras eller uppdateras.

Det finns en förkonfigurerad uppsättning mappningar mellan Azure AD-användarobjekt och objekt för varje SaaS-app. Vissa appar hantera andra typer av objekt, till exempel grupper eller kontakter. Välj en avbildning i tabell för att öppna Redigeraren för mappning till höger, där du kan visa och anpassa dem.

![Programmet resource fönstret](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Anpassningar som stöds är:

* Aktivera och inaktivera mappningar för specifika objekt, till exempel Azure AD-användarobjektet med SaaS-appens användarobjektet.
* Redigera attribut som flödar från Azure AD-användarobjektet till appens användarobjektet. Läs mer på attributmappning [förstå mappning attributtyper](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrering etablering åtgärderna som Azure AD som körs på det aktuella programmet. Du kan begränsa de åtgärder som utförs i stället för att Azure AD fullt Synkronisera objekt. 

  Exempelvis kan bara välja **uppdatering** och Azure AD endast säkerhetsuppdateringar befintliga konton i ett program men inte skapa nya. Välj bara **skapa** och Azure endast skapar nya användarkonton men inte uppdatera befintliga. Den här funktionen kan administratörer skapa olika mappningar för skapande av konto och uppdatera arbetsflöden.

* Lägger till en ny attributmappning. Välj **Lägg till ny mappning** längst ned på den **attributmappning** fönstret. Fyll i **redigera attributet** formuläret och välj **Ok** att lägga till ny mappning i listan. 

### <a name="settings"></a>Inställningar

Du kan starta och stoppa den Azure AD-etableringstjänsten för det valda programmet i den **inställningar** område i den **etablering** skärmen. Du kan också välja att rensa cacheminnet för etablering och starta om tjänsten.

Etablering har aktiverats för första gången för ett program, aktivera tjänsten genom att ändra den **Etableringsstatus** till **på**. Den här ändringen gör den Azure AD-etableringtjänsten att köra en inledande synkronisering. Det läser de användare som tilldelats i den **användare och grupper** avsnittet frågar målprogrammet för dem och kör sedan etablering åtgärder som definierats i Azure AD **mappningar** avsnittet. Under den här processen lagrar etableringstjänsten cachelagrade data om vilka användarkonton som hanteras så att icke-hanterade konton i målprogram som har aldrig inom omfånget för tilldelning inte påverkas av avetableringsförbättringar åtgärder. Efter den inledande synkroniseringen synkroniserar etableringstjänsten automatiskt användarobjektet och gruppobjektet med tio minuters intervall.

Ändra den **Etableringsstatus** till **av** att pausa etableringstjänsten. I det här tillståndet Azure inte skapa, uppdatera eller ta bort användare eller gruppobjekt i appen. Ändra tillståndet tillbaka till **på** och tjänsten hämtar där den avbröts.

Välj den **Avmarkera aktuell status och starta om synkronisering** kryssrutan och välj **spara** till:

* Stoppa etableringstjänsten
* Dumpa cachelagrade data om vilka konton som hanterar Azure AD
* Starta om tjänsterna och köra den första synkroniseringen igen

Det här alternativet kan användas av administratörer etablering distributionsprocessen börja om från början igen.

### <a name="synchronization-details"></a>Synkroniseringsinformation

Det här avsnittet innehåller ytterligare information om driften av etableringstjänsten, inklusive de första och sista etableringstjänsten körde programmet och hur många användare och gruppobjekt som den hanterar.

En länk till den **etablering aktivitetsrapporten**, som innehåller en logg över alla användare och grupper skapade, uppdaterade och borttagna mellan Azure AD och målprogrammet. Finns också en länk till den **etablering felrapport**, vilket ger mer detaljerade felmeddelanden för användare och grupp-objekt som inte kunde läsas, skapas, uppdateras eller tas bort.
