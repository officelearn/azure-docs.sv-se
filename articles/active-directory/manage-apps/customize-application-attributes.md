---
title: Anpassa attributmappningar för Azure AD | Microsoft Docs
description: Lär dig vilka attributmappningar för SaaS-appar i Azure Active Directory är hur du kan ändra dem för att åtgärda dina affärsbehov.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: barbkess
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ead99afaf0399c060424d310d8f4011b2a50aad2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473732"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Anpassa attributmappningar för Användaretablering för SaaS-program i Azure Active Directory
Microsoft Azure AD har stöd för användaretablering för SaaS-program från tredje part, till exempel Salesforce, Google Apps och andra. Om du har användaretablering för ett tredje parts SaaS-program som aktiverad, styr dess attributvärden i form av attributmappningar Azure-portalen.

Det finns en förkonfigurerad uppsättning attribut och attributmappningar mellan Azure AD-användarobjekt och objekt för varje SaaS-app. Vissa appar hantera andra typer av objekt och användare, till exempel grupper. <br> 
 Du kan anpassa attributmappningar standard enligt organisationens behov. Det innebär att du kan ändra eller ta bort befintliga attributmappningar eller skapa nya attributmappningar.
 
## <a name="editing-user-attribute-mappings"></a>Redigera användare attributmappningar

I Azure AD-portalen kan du komma åt den här funktionen genom att klicka på en **mappningar** konfigurationen under **etablering** i den **hantera** delen av en  **Företagsprogram**.


![Salesforce](./media/customize-application-attributes/21.png) 

Klicka på en **mappningar** konfiguration, öppnar det relaterade **attributmappning** skärmen. Det finns attributmappningar som krävs av ett SaaS-program ska fungera korrekt. För obligatoriska attribut i **ta bort** funktionen är inte tillgänglig.


![Salesforce](./media/customize-application-attributes/22.png)

I exemplet ovan ser du att den **användarnamn** attribut för ett hanterat objekt i Salesforce fylls i med de **userPrincipalName** värdet för den länkade Azure Active Directory-objektet.

Du kan anpassa befintliga **attributmappningar** genom att klicka på en mappning. Då öppnas det **redigera attributet** skärmen.

![Salesforce](./media/customize-application-attributes/23.png)


### <a name="understanding-attribute-mapping-types"></a>Förstå attributmappning typer
Med attributmappningar styra du hur attribut fylls i ett SaaS-program från tredje part. Det finns fyra olika Mappningstyper som stöds:

* **Direkt** – Målattributet fylls i med värdet för ett attribut för det länkade objektet i Azure AD.
* **Konstant** – Målattributet fylls med en specifik sträng som du har angett.
* **Uttrycket** -Målattributet har fyllts i baserat på resultatet av ett skript-liknande uttryck. 
  Mer information finns i [skriva uttryck för attributmappningar i Azure Active Directory](functions-for-customizing-application-data.md).
* **Ingen** -Målattributet lämnas oförändrade. Men om Målattributet är någonsin tom fylls det i med standardvärdet som du anger.

Förutom dessa fyra grundläggande typer, stöd för anpassade attributmappningar konceptet med en valfri **standard** värde tilldelning. Default-tilldelningen värdet garanterar att ett målattribut fylls med ett värde om det finns ingen ett värde i Azure AD inte heller i målobjektet. Den vanligaste konfigurationen är att lämna fältet tomt.


### <a name="understanding-attribute-mapping-properties"></a>Förstå attributmappning egenskaper

I det föregående avsnittet har du redan har lagts till attributmappning type-egenskapen.
Förutom den här egenskapen stöder också attributmappningar följande attribut:

- **Källattributet** -användarattribut från källsystemet (exempel: Azure Active Directory).
- **Målattribut** – användarattribut i målsystemet (exempel: ServiceNow).
- **Matcha objekt med hjälp av det här attributet** – oavsett om den här mappningen ska användas för att unikt identifiera användare mellan käll-och mål. Detta är vanligtvis inställt på attributet userPrincipalName eller e-post i Azure AD, som vanligtvis är mappad till ett fält för användarnamn i ett målprogram.
- **Matchningsprioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som anges av det här fältet. När en matchning hittas, matchar ingen ytterligare attribut utvärderas.
- **Tillämpa den här mappningen**
    - **Alltid** – tillämpa den här mappningen för både skapa användare och uppdatera åtgärder
    - **Endast när skapas** -tillämpa den här mappningen endast på åtgärder för skapande av användare


## <a name="editing-group-attribute-mappings"></a>Redigera grupp attributmappningar

Ett valt antal program, till exempel ServiceNow, Box och Google Apps, stöd för möjligheten att etablera gruppobjekt förutom användarobjekt. Gruppera objekt kan innehålla egenskaper, till exempel visningsnamn och e-alias, förutom att medlemmar i gruppen.

![ServiceNow](./media/customize-application-attributes/24.png)

Gruppetablering kan också aktiveras eller inaktiveras genom att välja mappningen av enhetsgrupp under **mappningar**, och inställningen **aktiverad** till önskat alternativ i den **attributmappning** skärmen.

De attribut som etablerats som en del av gruppobjekt kan anpassas på samma sätt som objekt, som beskrivs ovan. 

>[!TIP]
>Etablering av grupp-objekt (egenskaper och medlemmar) är ett distinkt koncept från [tilldela grupper](assign-user-or-group-access-portal.md) till ett program. Det är möjligt att tilldela en grupp till ett program, men bara etablera användarobjekt som ingår i gruppen. Etablering av fullständig gruppobjekt krävs inte för att använda grupper i tilldelningar.


## <a name="editing-the-list-of-supported-attributes"></a>Redigera listan över attribut som stöds

Användarattribut som stöds för ett visst program är förkonfigurerad. Användarhantering för de flesta program API: er har inte stöd för identifiering av schemat, den Azure AD-etableringstjänsten är därför inte kan dynamiskt generera listan över attribut som stöds genom att göra anrop till programmet. 

Vissa program har dock stöd för anpassade attribut. För den Azure AD-etableringstjänsten för att kunna läsa och skriva till anpassade attribut, deras definitioner måste anges i Azure portal med den **visa avancerade alternativ** kryssrutan längst ned på den  **Attributmappning** skärmen.

Program och system som har stöd för anpassning av attributlistan är:

* Salesforce
* ServiceNow
* Workday
* Azure Active Directory ([Azure AD Graph API standardattribut](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) och anpassade katalogtillägg som stöds)
* Appar som stöder [SCIM 2.0](https://tools.ietf.org/html/rfc7643), där attribut som definierats i den [grundläggande schemat](https://tools.ietf.org/html/rfc7643) måste läggas till

>[!NOTE]
>Redigera listan över attribut som stöds rekommenderas endast för administratörer som har anpassat schemat för sina program och system, och som har erfarenhet kunskaper om hur deras anpassade attribut har definierats. Detta kräver ibland kunskap om de verktyg för API: er och utvecklare som tillhandahålls av ett program eller system. 

![Redigeringsprogram](./media/customize-application-attributes/25.png) 

När du redigerar listan över attribut som stöds, finns följande egenskaper:

* **Namn på** -systemnamnet för attributet som definierats i målobjektets schemat. 
* **Typ** -vilken typ av data som attributet lagrar, enligt definitionen i målobjektets schemat. Detta kan vara något av följande:
   * *Binär* -attributet som innehåller binära data.
   * *Booleska* -attributet innehåller ett värde för SANT eller FALSKT.
   * *DateTime* -attributet innehåller en datumsträng.
   * *Heltal* -attributet innehåller ett heltal.
   * *Referens för* -attributet innehåller ett ID som refererar till ett värde som lagras i en annan tabell i målprogrammet.
   * *Sträng* -attributet innehåller en textsträng. 
* **Primär nyckel?** – Huruvida attributet definieras som ett primärt nyckelfält i målobjektets schemat.
* **Krävs?** – Huruvida attributet krävs att fyllas i målprogrammet eller system.
* **Flera värden?** – Huruvida attributet har stöd för flera värden.
* **Skiftlägeskänsligt?** – Huruvida utvärderas attribut-värden i ett skiftlägeskänsligt sätt.
* **API-uttryck** – Använd inte, om inte uppmanas att göra det genom att i dokumentationen för en viss etablering koppling (till exempel Workday).
* **Refererar till objektattribut** – om det här är ett referensattribut typ, och sedan på den här menyn kan du välja tabellen och attribut i målprogrammet med det värde som är associerade med attributet. Till exempel, om du har ett attribut med namnet ”avdelning” vars lagrade värdet refererar till ett objekt i en separat tabell för ”avdelningar”, väljer du ”Departments.Name”. Observera att referenstabeller och de primära ID-fält som stöds för ett visst program redan har konfigurerats och för närvarande kan inte redigeras med Azure-portalen, men kan redigeras med den [Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Om du vill lägga till ett nytt attribut, bläddra till slutet av listan över attribut som stöds, fyller du i fälten ovan med de angivna indata och välj **lägga till attributet**. Välj **spara** när du är klar att lägga till attribut. Du sedan måste du uppdatera den **etablering** fliken för de nya attribut ska bli tillgänglig i attributmappning redigeraren.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Återställer standardattribut och attributmappningar

Om du behöver starta över och återställning av dina befintliga mappningar tillbaka till standardläget, kan du välja den **återställa standardmappningar** kryssrutan och spara konfigurationen. Detta anger alla mappningar som om programmet bara lagts till i Azure AD-klienten från appgalleriet. 

Det här alternativet tvingar effektivt omsynkronisering av alla användare medan etableringstjänsten körs. 

>[!IMPORTANT]
>Vi rekommenderar starkt som **Etableringsstatus** anges till **av** innan du anropar det här alternativet.


## <a name="what-you-should-know"></a>Det här bör du känna till

* Microsoft Azure AD tillhandahåller en effektiv implementering av en synkroniseringsprocessen. I en initierad miljö bearbetas bara de objekt som kräver uppdateringar under en synkroniseringscykel. 

* Uppdaterar attributmappningar har en inverkan på prestanda hos en synkroniseringscykel. En uppdatering av attributmappning konfigurationen kräver alla hanterade objekt som ska vara omvärderas. 

* Det är rekommenderad praxis att hålla din attributmappningar lägst antal på varandra följande ändringar.


## <a name="next-steps"></a>Nästa steg

* [Automatisera användaren etablering/avetablering för SaaS-appar](user-provisioning.md)
* [Skriva uttryck för attributmappningar](functions-for-customizing-application-data.md)
* [Omfångsfilter för etableringen av användare](define-conditional-rules-for-provisioning-user-accounts.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md)
* [Lista över guider om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)


