---
title: Anpassa Azure AD-attributmappning | Microsoft Docs
description: Lär dig vilka attributmappning för SaaS-appar i Azure Active Directory är hur du kan ändra dem för att adressera dina affärsbehov.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 565394664ab59ef5186503f708502eacc040321f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295633"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Anpassa attributmappning för Användaretablering för SaaS-program i Azure Active Directory
Microsoft Azure AD har stöd för användaretablering för SaaS-program från tredje part, till exempel Salesforce, Google Apps och andra. Om du har användaretablering för en tredje parts SaaS-program aktiverat kontrollerar Azure-portalen dess attributvärden i form av en konfiguration som kallas ”attributmappning”.

Det finns en förkonfigurerad uppsättning attribut och attributmappningar mellan användarobjekt i Azure AD och användarobjekt för varje SaaS-app. Vissa appar hantera andra typer av objekt förutom användare, till exempel grupper. <br> 
 Du kan anpassa attributet standardmappningar enligt dina behov. Detta innebär kan du ändra eller ta bort befintliga attributmappningar för eller skapa nya attributmappning.
 
## <a name="editing-user-attribute-mappings"></a>Redigera mappningar av användaren

I Azure AD-portalen kan du komma åt den här funktionen genom att klicka på en **mappningar** konfiguration under **etablering** i den **hantera** avsnitt i en **företagsprogram**.


![Salesforce][5] 

Klicka på en **mappningar** konfiguration, öppnar det relaterade **attributmappning** skärmen. Det finns attributmappning som krävs av ett SaaS-program ska fungera korrekt. För obligatoriska attribut i **ta bort** funktionen är inte tillgänglig.


![Salesforce][6]  

I exemplet ovan kan du se att den **användarnamn** attribut för ett hanterat objekt i Salesforce fylls med den **userPrincipalName** värdet för den länkade Azure Active Directory-objekt.

Du kan anpassa befintliga **attributmappning** genom att klicka på en mappning. Då öppnas den **Redigera attribut** skärmen.

![Salesforce][7]  


### <a name="understanding-attribute-mapping-types"></a>Förstå attributtyper mappning
Med attributmappning styra hur attribut fylls i ett SaaS-program från tredje part. Det finns fyra olika Mappningstyper som stöds:

* **Direkt** – Målattributet fylls i med värdet för ett attribut för det länkade objektet i Azure AD.
* **Konstant** – Målattributet fylls med en specifik sträng som du har angett.
* **Uttrycket** -Målattributet fylls i baserat på resultatet av ett skript-liknande uttryck. 
  Mer information finns i [skriva uttryck för attributmappning i Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Ingen** -Målattributet lämnas oförändrade. Men om Målattributet är någonsin tom fylls det i med standardvärdet som du anger.

Förutom dessa fyra grundläggande attributet Mappningstyper anpassade attributmappning stöder begreppet en valfri **standard** värdet tilldelning. Default-tilldelningen värdet säkerställer att målattribut fylls med ett värde om det finns varken ett värde i Azure AD eller i målobjektet. Den vanligaste konfigurationen är att du lämnar fältet tomt.


### <a name="understanding-attribute-mapping-properties"></a>Förstå egenskaper för mappning av attribut

I det föregående avsnittet har du redan har lagts till mappningen type-egenskapen för attributet.
Förutom den här egenskapen stöder också attributmappning följande attribut:

- **Källattributet** -användarattribut från källsystemet (exempel: Azure Active Directory).
- **Målattribut** – användarattribut i målsystemet (exempel: ServiceNow).
- **Matcha objekt med hjälp av det här attributet** – oavsett om den här mappningen som ska användas för att unikt identifiera användarna mellan käll-och mål. Detta anges normalt i Azure AD, som vanligtvis är mappad till ett fält för användarnamn i ett målprogram på attributet userPrincipalName eller e-post.
- **Matchar prioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som anges av det här fältet. När en matchning hittas matchar ingen ytterligare attribut utvärderas.
- **Använd den här mappningen**
    - **Alltid** – tillämpa den här mappningen för både skapa användare och uppdatera åtgärder
    - **Endast under skapa** -mappningen tillämpas endast på användare creation-åtgärder


## <a name="editing-group-attribute-mappings"></a>Redigera mappningar av grupp

Ett urval av program, till exempel ServiceNow och rutan Google Apps stöd för möjligheten att etablera gruppobjekt förutom användarobjekt. Gruppera objekt kan innehålla egenskaper, till exempel visningsnamn och e-alias, förutom gruppmedlemmar.

![ServiceNow][8]  

Gruppen etablering kan eventuellt aktiveras eller inaktiveras genom att välja Gruppmappning under **mappningar**, och inställningen **aktiverad** till önskade alternativ i den **attributmappning** skärmen.

De attribut som etablerats som en del av gruppobjekt kan anpassas på samma sätt som användarobjekt som beskrivs ovan. 

>[!TIP]
>Etablering av gruppobjekt (egenskaper och medlemmar) är ett begrepp som är skilda från [tilldela grupper](manage-apps/assign-user-or-group-access-portal.md) till ett program. Det är möjligt att tilldela en grupp till ett program, men endast etablera användarobjekt som ingår i gruppen. Etablering av fullständig gruppobjekt krävs inte för att använda grupper i tilldelningar.


## <a name="editing-the-list-of-supported-attributes"></a>Redigera lista över attribut som stöds

Användarattribut som stöds för ett visst program har redan konfigurerats. De flesta program Användarhantering API: er har inte stöd för identifiering av schemat, därför Azure AD Etablerar tjänsten går inte att dynamiskt Generera lista över attribut som stöds genom att göra anrop till programmet. 

Vissa program stöder dock anpassade attribut. För att etablera tjänsten Azure AD för att kunna läsa och skriva till anpassade attribut, deras definitioner måste anges i Azure portal med den **visa avancerade alternativ** kryssrutan längst ned i den  **Attributet mappning** skärmen.

Program och system som stöder anpassning av attributlistan omfattar:

* Salesforce
* ServiceNow
* Arbetsdagar
* Azure Active Directory
* Lokala Active Directory (som en del av anslutningen Workday användaretablering)
* Appar som stöder [SCIM 2.0](https://tools.ietf.org/html/rfc7643), där attribut som definierats i den [grundläggande schemat](https://tools.ietf.org/html/rfc7643) måste läggas till

>[!NOTE]
>Redigera lista över attribut som stöds rekommenderas endast för administratörer som har anpassat schemat för sina program och system och själv kunskaper om hur de anpassade attributen har definierats. Detta kräver ibland kunskap om de API: er och utvecklare verktyg som tillhandahålls av ett program eller system. 

![Redigeringsprogram][9]  

När du redigerar lista över attribut som stöds, finns följande egenskaper:

* **Namnet** -systemnamn för attributet som har definierats i schemat för målobjektets. 
* **Typen** -datatypen attributet lagras, enligt definitionen i målobjektets schemat. Detta kan vara något av följande:
   * *Binär* -attributet innehåller binära data.
   * *Booleskt* -attributet som innehåller värdet SANT eller FALSKT.
   * *DateTime* -attributet innehåller en datumsträng.
   * *Heltal* -attributet innehåller ett heltal.
   * *Referens* -attributet innehåller ett ID som refererar till ett värde som lagras i en annan tabell i målprogrammet.
   * *Strängen* -attributet innehåller en textsträng. 
* **Primärnyckeln?** -Om huruvida attributet definieras som en primärnyckel fält i objektet target schemat.
* **Krävs?** -Om huruvida attributet är måste fyllas i målprogrammet eller system.
* **Flera värden?** -Om huruvida attributet har stöd för flera värden.
* **Exakta skiftläget?** -Om huruvida utvärderas attribut-värden i ett skiftlägeskänsligt sätt.
* **API-uttrycket** – Använd inte, om du uppmanas att göra det av dokumentationen för en viss etablering koppling (till exempel Workday).
* **Refererar till attributet** – om det här är ett referensattribut typ, och sedan på den här menyn kan du välja tabellen och attribut i målprogrammet som innehåller värdet som är associerade med attributet. Om du har ett attribut med namnet ”avdelning” vars lagrade värdet refererar till ett objekt i en separat ”avdelningar” tabell, skulle du till exempel väljer ”Departments.Name”. Observera att referens-tabeller och de primära ID-fält som stöds för ett visst program är förkonfigurerad för närvarande inte kan redigeras i Azure-portalen och kan redigeras med den [Graph API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Rulla till slutet av listan över de attribut som stöds för att lägga till ett nytt attribut, Fyll i fälten ovan med angivna indata och väljer **lägga till attributet**. Välj **spara** när du är klar att lägga till attribut. Du sedan måste du uppdatera den **etablering** för nya attribut ska bli tillgänglig i attributet mappning redigeraren.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Återställer standardattributen och attributmappning

Om du behöver starta över och återställning av din befintliga mappningar tillbaka till sitt ursprungsläge, kan du välja den **återställa standardmappningar** kryssrutan och spara konfigurationen. Detta anger alla mappningar som om programmet precis lagts till i Azure AD-klienten från programgalleriet. 

Det här alternativet tvingas effektivt omsynkronisering av alla användare när etablering tjänsten igång. 

>[!IMPORTANT]
>Vi rekommenderar starkt som **Etableringsstatusen** anges till **av** innan du anropar det här alternativet.


## <a name="what-you-should-know"></a>Vad du bör känna till

* Microsoft Azure AD tillhandahåller en effektiv implementering av processen för synkronisering. I en miljö som initierats bearbetas bara de objekt som kräver uppdateringar under en synkroniseringscykel. 

* Uppdatera attributmappning påverkar prestandan hos en synkroniseringscykel. En uppdatering för attributet mappningskonfigurationen kräver alla hanterade objekt som ska vara reevaluated. 

* Det är rekommenderad praxis att hålla din attributmappning minst antal på varandra följande ändringar.


## <a name="next-steps"></a>Nästa steg

* [Automatisera användaren etablering/avetablering för SaaS-appar](active-directory-saas-app-provisioning.md)
* [Skriva uttryck för attributmappning](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Omfångsfilter för Användaretablering](active-directory-saas-scoping-filters.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](manage-apps/use-scim-to-provision-users-and-groups.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](active-directory-saas-tutorial-list.md)

<!--Image references-->
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png
[8]: ./media/active-directory-saas-customizing-attribute-mappings/24.png
[9]: ./media/active-directory-saas-customizing-attribute-mappings/25.PNG

