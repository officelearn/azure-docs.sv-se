---
title: Anpassa Azure AD-attributmappning | Microsoft Docs
description: "Lär dig vilka attributmappning för SaaS-appar i Azure Active Directory är hur du kan ändra dem för att adressera dina affärsbehov."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ed3558fd4ca0022389091edad9caa7686a5116a0
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Anpassa attributmappning för Användaretablering för SaaS-program i Azure Active Directory
Microsoft Azure AD har stöd för användaretablering för SaaS-program från tredje part, till exempel Salesforce, Google Apps och andra. Om du har användaretablering för en tredje parts SaaS-program aktiverat styr Azure-hanteringsportalen dess attributvärden i form av en konfiguration som kallas ”attributmappning”.

Det finns en förkonfigurerad uppsättning mappningar mellan användarobjekt i Azure AD och användarobjekt för varje SaaS-app. Vissa appar hantera andra typer av objekt, till exempel grupper eller kontakter. <br> 
 Du kan anpassa attributet standardmappningar enligt dina behov. Detta innebär, kan du ändra eller ta bort befintliga attributmappningar för eller skapa nya attributmappning.

I Azure AD-portalen kan du komma åt den här funktionen genom att klicka på en **mappningar** konfiguration under **etablering** i den **hantera** avsnitt i en **företagsprogram**.


![Salesforce][5] 

Klicka på en **mappningar** konfiguration, öppnar det relaterade **attributmappning** bladet.  
Det finns attributmappning som krävs av ett SaaS-program ska fungera korrekt. För obligatoriska attribut i **ta bort** funktionen är inte tillgänglig.


![Salesforce][6]  

I exemplet ovan kan du se att den **användarnamn** attribut för ett hanterat objekt i Salesforce fylls med den **userPrincipalName** värdet för den länkade Azure Active Directory-objekt.

Du kan anpassa befintliga **attributmappning** genom att klicka på en mappning. Då öppnas den **Redigera attribut** bladet.

![Salesforce][7]  


  

## <a name="understanding-attribute-mapping-types"></a>Förstå attributtyper mappning
Med attributmappning styra hur attribut fylls i ett SaaS-program från tredje part. Det finns fyra olika Mappningstyper som stöds:

* **Direkt** – Målattributet fylls i med värdet för ett attribut för det länkade objektet i Azure AD.
* **Konstant** – Målattributet fylls med en specifik sträng som du har angett.
* **Uttrycket** -Målattributet fylls i baserat på resultatet av ett skript-liknande uttryck. 
  Mer information finns i [skriva uttryck för attributmappning i Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Ingen** -Målattributet lämnas oförändrade. Men om Målattributet är någonsin tom fylls det i med standardvärdet som du anger.

Förutom dessa fyra grundläggande attributet Mappningstyper anpassade attributmappning stöder begreppet en valfri **standard** värdet tilldelning. Default-tilldelningen värdet säkerställer att målattribut fylls med ett värde om det finns varken ett värde i Azure AD eller i målobjektet. Den vanligaste konfigurationen är att du lämnar fältet tomt.


## <a name="understanding-attribute-mapping-properties"></a>Förstå egenskaper för mappning av attribut

I det föregående avsnittet har du redan har lagts till mappningen type-egenskapen för attributet.
Förutom den här egenskapen stöder också attributmappning följande attribut:

- **Källattributet** -användarattribut från källsystemet (t.ex.: Azure Active Directory).
- **Målattribut** – användarattribut i målsystemet (t.ex.: ServiceNow).
- **Matcha objekt med hjälp av det här attributet** – oavsett om den här mappningen som ska användas för att unikt identifiera användarna mellan käll-och mål. Detta anges normalt i Azure AD, som vanligtvis är mappad till ett fält för användarnamn i ett målprogram på attributet userPrincipalName eller e-post.
- **Matchar prioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som anges av det här fältet. När en matchning hittas matchar ingen ytterligare attribut utvärderas.
- **Använd den här mappningen**
    - **Alltid** – tillämpa den här mappningen för både skapa användare och uppdatera åtgärder
    - **Endast under skapa** -mappningen tillämpas endast på användare creation-åtgärder


## <a name="what-you-should-know"></a>Vad du bör känna till

Microsoft Azure AD tillhandahåller en effektiv implementering av processen för synkronisering. I en miljö som initierats bearbetas bara de objekt som kräver uppdateringar under en synkroniseringscykel. Uppdatera attributmappning påverkar prestandan hos en synkroniseringscykel. En uppdatering för attributet mappningskonfigurationen kräver alla hanterade objekt som ska vara reevaluated. Det är rekommenderad praxis att hålla din attributmappning minst antal på varandra följande ändringar.

## <a name="next-steps"></a>Nästa steg

* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Automatisera användaren etablering/avetablering för SaaS-appar](active-directory-saas-app-provisioning.md)
* [Skriva uttryck för attributmappning](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Omfångsfilter för Användaretablering](active-directory-saas-scoping-filters.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](active-directory-scim-provisioning.md)
* [Kontot etablering meddelanden](active-directory-saas-account-provisioning-notifications.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png

