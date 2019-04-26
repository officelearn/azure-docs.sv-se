---
title: Anpassa attributmappningar för Azure AD | Microsoft Docs
description: Lär dig vilka attributmappningar för SaaS-appar i Azure Active Directory är hur du kan ändra dem för att åtgärda dina affärsbehov.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: celested
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a2965fecd3aca17d6c4df7e49ad466377de9762
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291695"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Anpassa attributmappningar för Användaretablering för SaaS-program i Azure Active Directory
Microsoft Azure AD har stöd för användaretablering för SaaS-program från tredje part, till exempel Salesforce, Google Apps och andra. Om du aktiverar användaretablering för ett SaaS-program från tredje part, styr dess attributvärden via attributmappningar Azure-portalen.

Det finns en förkonfigurerad uppsättning attribut och attributmappningar mellan Azure AD-användarobjekt och objekt för varje SaaS-app. Vissa appar hantera andra typer av objekt tillsammans med användare, till exempel grupper.

Du kan anpassa attributmappningar standard enligt organisationens behov. Därför kan du ändra eller ta bort befintliga attributmappningar eller skapa nya attributmappningar.
 
## <a name="editing-user-attribute-mappings"></a>Redigera användare attributmappningar

Följ dessa steg för att komma åt den **mappningar** funktion i etableringen av användare:

1. Logga in på den [Azure Active Directory-portalen](https://aad.portal.azure.com).

1. Välj **företagsprogram** i den vänstra rutan. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.

1. Välj en app för att läsa in dess app fönstret hantering, där du kan visa rapporter och hantera appinställningar.

1. Välj **etablering** att hantera användarkonto etableringsinställningar för den valda appen.

1. Expandera **mappningar** att visa och redigera användarattribut som flödar mellan Azure AD och målprogrammet. Om målprogrammet stöder det kan kan det här avsnittet du också konfigurera etablering av grupper och användarkonton.

   ![Salesforce](./media/customize-application-attributes/21.png) 

1. Välj en **mappningar** konfiguration för att öppna den relaterade **attributmappning** skärmen. Vissa attributmappningar krävs av ett SaaS-program ska fungera korrekt. För obligatoriska attribut i **ta bort** funktionen är inte tillgänglig.

   ![Salesforce](./media/customize-application-attributes/22.png)

   I den här skärmbilden ser du att den **användarnamn** attribut för ett hanterat objekt i Salesforce fylls i med de **userPrincipalName** värdet för den länkade Azure Active Directory-objektet.

1. Välj en befintlig **attributmappning** att öppna den **redigera attributet** skärmen. Här kan du redigera användarattribut som flödar mellan Azure AD och målprogrammet.

   ![Salesforce](./media/customize-application-attributes/23.png)


### <a name="understanding-attribute-mapping-types"></a>Förstå attributmappning typer
Med attributmappningar styra du hur attribut fylls i ett SaaS-program från tredje part. Det finns fyra olika Mappningstyper som stöds:

* **Direkt** – Målattributet fylls i med värdet för ett attribut för det länkade objektet i Azure AD.
* **Konstant** – Målattributet fylls med en specifik sträng som du angav.
* **Uttrycket** -Målattributet har fyllts i baserat på resultatet av ett skript-liknande uttryck. 
  Mer information finns i [skriva uttryck för attributmappningar i Azure Active Directory](functions-for-customizing-application-data.md).
* **Ingen** -Målattributet lämnas oförändrade. Men om Målattributet är någonsin tom fylls det i med standardvärdet som du anger.

Tillsammans med de här fyra grundläggande typer, anpassade attributmappningar stöder konceptet med en valfri **standard** värde tilldelning. Default-tilldelningen värdet garanterar att ett målattribut fylls med ett värde om det inte är ett värde i Azure AD eller hos målobjektet. Den vanligaste konfigurationen är att lämna fältet tomt.


### <a name="understanding-attribute-mapping-properties"></a>Förstå attributmappning egenskaper

I det föregående avsnittet berättade redan attributmappning type-egenskapen.
Tillsammans med den här egenskapen stöder attributmappningar även följande attribut:

- **Källattributet** -användarattribut från källsystemet (exempel: Azure Active Directory).
- **Målattribut** – användarattribut i målsystemet (exempel: ServiceNow).
- **Matcha objekt med hjälp av det här attributet** – om den här mappningen ska användas för att unikt identifiera användare mellan käll-och mål. Det anges normalt i Azure AD, som vanligtvis är mappad till ett fält för användarnamn i ett målprogram på attributet userPrincipalName eller e-post.
- **Matchningsprioritet** – flera matchande attribut kan anges. När det finns flera, är de utvärderas i den ordning som anges av det här fältet. När en matchning hittas, matchar ingen ytterligare attribut utvärderas.
- **Tillämpa den här mappningen**
    - **Alltid** – tillämpa den här mappningen för både skapa användare och uppdatera åtgärder.
    - **Endast när skapas** -tillämpa den här mappningen endast på åtgärder för skapande av användare.


## <a name="editing-group-attribute-mappings"></a>Redigera grupp attributmappningar

Ett valt antal program, till exempel ServiceNow, Box och Google Apps, stöd för möjligheten att etablera gruppobjekt och användarobjekt. Gruppera objekt kan innehålla egenskaper, till exempel visningsnamn och e-alias, tillsammans med medlemmar i gruppen.

![ServiceNow](./media/customize-application-attributes/24.png)

Gruppetablering kan också aktiveras eller inaktiveras genom att välja mappningen av enhetsgrupp under **mappningar**, och inställningen **aktiverad** till ett alternativ i den **attributmappning** skärmen.

De attribut som etablerats som en del av gruppobjekt kan anpassas på samma sätt som objekt, som beskrivs ovan. 

>[!TIP]
>Etablering av grupp-objekt (egenskaper och medlemmar) är ett distinkt koncept från [tilldela grupper](assign-user-or-group-access-portal.md) till ett program. Det är möjligt att tilldela en grupp till ett program, men bara etablera användarobjekt som ingår i gruppen. Etablering av fullständig gruppobjekt krävs inte för att använda grupper i tilldelningar.


## <a name="editing-the-list-of-supported-attributes"></a>Redigera listan över attribut som stöds

Användarattribut som stöds för ett visst program är förkonfigurerad. De flesta program Användarhantering API: er stöder inte schemat identifiering. Den Azure AD-etableringtjänsten har därför inte att dynamiskt generera listan över attribut som stöds genom att göra anrop till programmet. 

Men vissa program har stöd för anpassade attribut och den Azure AD-etableringtjänsten kan läsa och skriva till anpassade attribut. Om du vill ange deras definitioner i Azure portal, Välj den **visa avancerade alternativ** kryssrutan längst ned på den **attributmappning** skärmen och välj sedan **redigera attributlista för** din app.

Program och system som har stöd för anpassning av attributlistan är:

* Salesforce
* ServiceNow
* Workday
* Azure Active Directory ([Azure AD Graph API standardattribut](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) och anpassade katalogtillägg som stöds)
* Appar som stöder [SCIM 2.0](https://tools.ietf.org/html/rfc7643), där attribut som definierats i den [grundläggande schemat](https://tools.ietf.org/html/rfc7643) måste läggas till

>[!NOTE]
>Redigera listan över attribut som stöds rekommenderas endast för administratörer som har anpassat schemat för sina program och system, och som har erfarenhet kunskaper om hur deras anpassade attribut har definierats. Detta kräver ibland kunskap om de API: er och utvecklare verktyg som tillhandahålls av ett program eller system. 

När du redigerar listan över attribut som stöds, finns följande egenskaper:

* **Namn på** -systemnamnet för attributet som definierats i målobjektets schemat. 
* **Typ** -vilken typ av data attributet lagrar, enligt definitionen i målobjektets schema, som kan vara något av följande typer:
   * *Binär* -attributet som innehåller binära data.
   * *Booleska* -attributet innehåller ett värde för SANT eller FALSKT.
   * *DateTime* -attributet innehåller en datumsträng.
   * *Heltal* -attributet innehåller ett heltal.
   * *Referens för* -attributet innehåller ett ID som refererar till ett värde som lagras i en annan tabell i målprogrammet.
   * *Sträng* -attributet innehåller en textsträng. 
* **Primär nyckel?** – Om attributet definieras som ett primärt nyckelfält i målobjektets schemat.
* **Krävs?** – Om attributet krävs att fyllas i målprogrammet eller system.
* **Flera värden?** – Om attributet har stöd för flera värden.
* **Skiftlägeskänsligt?** – Om utvärderas attribut-värden i ett skiftlägeskänsligt sätt.
* **API-uttryck** -inte använda inte uppmanas att göra det genom att i dokumentationen för en viss etablering koppling (till exempel Workday).
* **Refererar till objektattribut** – om det är ett referensattribut typ, och sedan den här menyn kan du markera tabellen och attribut i målprogrammet med det värde som är associerade med attributet. Till exempel, om du har ett attribut med namnet ”avdelning” vars lagrade värdet refererar till ett objekt i en separat tabell för ”avdelningar”, väljer du ”Departments.Name”. Referenstabeller och de primära ID-fält som stöds för ett visst program redan har konfigurerats och för närvarande kan inte redigeras med Azure-portalen, men kan redigeras med den [Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Om du vill lägga till ett nytt attribut, bläddra till slutet av listan över attribut som stöds, fyller du i fälten ovan med de angivna indata och välj **lägga till attributet**. Välj **spara** när du är klar att lägga till attribut. Du sedan måste du uppdatera den **etablering** fliken för de nya attribut ska bli tillgänglig i attributmappning redigeraren.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Återställer standardattribut och attributmappningar

Om du behöver starta över och återställning av dina befintliga mappningar tillbaka till standardläget, kan du välja den **återställa standardmappningar** kryssrutan och spara konfigurationen. Detta anger alla mappningar som om programmet bara har lagts till i Azure AD-klienten från appgalleriet. 

Det här alternativet tvingar effektivt en omsynkronisering av alla användare medan etableringstjänsten körs. 

>[!IMPORTANT]
>Vi rekommenderar starkt att **Etableringsstatus** anges till **av** innan du anropar det här alternativet.


## <a name="what-you-should-know"></a>Det här bör du känna till

* Microsoft Azure AD tillhandahåller en effektiv implementering av en synkroniseringsprocessen. I en initierad miljö bearbetas bara de objekt som kräver uppdateringar under en synkroniseringscykel. 

* Uppdaterar attributmappningar har en inverkan på prestanda hos en synkroniseringscykel. En uppdatering av attributmappning konfigurationen kräver alla hanterade objekt som ska vara omvärderas. 

* En rekommenderad metod är att hålla din attributmappningar lägst antal på varandra följande ändringar.


## <a name="next-steps"></a>Nästa steg

* [Automatisera användaren etablering/avetablering för SaaS-appar](user-provisioning.md)
* [Skriva uttryck för attributmappningar](functions-for-customizing-application-data.md)
* [Omfångsfilter för etableringen av användare](define-conditional-rules-for-provisioning-user-accounts.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md)
* [Lista över guider om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)


