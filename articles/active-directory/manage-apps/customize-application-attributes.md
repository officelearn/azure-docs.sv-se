---
title: Anpassa mappningar av Azure AD-attribut | Microsoft Docs
description: Lär dig mer om vilka filmappningar för SaaS-appar i Azure Active Directory hur du kan ändra dem så att de passar dina affärs behov.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef3d6a47986056925f9964638c9c7192341ca5f9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240999"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Anpassa attribut för användar etablering för SaaS-program i Azure Active Directory

Microsoft Azure AD ger stöd för användar etablering för SaaS-program från tredje part, till exempel Salesforce, G Suite och andra. Om du aktiverar användar etablering för ett SaaS-program från tredje part styr Azure Portal dess attributvärden via attribut-mappningar.

Det finns en förkonfigurerad uppsättning attribut och attribut mappningar mellan Azure AD-användarkonton och varje SaaS-Apps användar objekt. Vissa appar hanterar andra typer av objekt tillsammans med användare, t. ex. grupper.

Du kan anpassa standardattributen – mappningar efter dina affärs behov. Så du kan ändra eller ta bort befintliga attribut-mappningar eller skapa nya attribut-mappningar.

## <a name="editing-user-attribute-mappings"></a>Redigera användarattribut – mappningar

Följ de här stegen för att få åtkomst till **mappnings** funktionen för användar etablering:

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com).
1. Välj **företags program** i det vänstra fönstret. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.
1. Välj en app för att läsa in fönstret för hantering av appar där du kan visa rapporter och hantera appinställningar.
1. Välj **etablering** för att hantera inställningar för etablering av användar konto för den valda appen.
1. Expandera **mappningar** för att visa och redigera användarattribut som flödar mellan Azure AD och mål programmet. Om mål programmet stöder det kan du med hjälp av det här avsnittet Konfigurera etablering av grupper och användar konton.

   ![Använd mappningar för att visa och redigera användarattribut](./media/customize-application-attributes/21.png)

1. Välj en **mappnings** konfiguration för att öppna skärmen relaterad **Mappning av attribut** . Vissa attribut-mappningar krävs av ett SaaS-program för att fungera korrekt. **Borttagnings** funktionen är inte tillgänglig för obligatoriska attribut.

   ![Använd attributmappning för att konfigurera attribut mappningar för appar](./media/customize-application-attributes/22.png)

   I den här skärm bilden kan du se att attributet **username** för ett hanterat objekt i Salesforce fylls med värdet **userPrincipalName** för det länkade Azure Active Directory-objektet.

1. Välj en befintlig **attributmappning** för att öppna skärmen **Redigera attribut** . Här kan du redigera användarattribut som flödar mellan Azure AD och mål programmet.

   ![Använd Edit-attribut för att redigera användarattribut](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Förstå attribut-mappnings typer

Med attribut-mappningar kan du styra hur attribut fylls i i ett SaaS program från tredje part.
Det finns fyra olika mappnings typer som stöds:

- **Direct** – målattributet är ifyllt med värdet för ett attribut för det länkade objektet i Azure AD.
- **Konstant** – målattributet är ifyllt med en specifik sträng som du har angett.
- **Uttryck** – målattributet är ifyllt baserat på resultatet av ett skript som liknar uttryck.
  Mer information finns i [skriva uttryck för attribut-mappningar i Azure Active Directory](functions-for-customizing-application-data.md).
- **Inget** – målattributet lämnas oförändrat. Men om målattributet skulle vara tomt fylls det med det standardvärde som du anger.

Tillsammans med de här fyra grundläggande typerna stöder anpassade attribut-mappningar konceptet med en valfri **Standardvärde** tilldelning. Standardvärdes tilldelningen säkerställer att ett målattribut fylls med ett värde om det inte finns ett värde i Azure AD eller på målobjektet. Den vanligaste konfigurationen är att lämna detta tomt.

### <a name="understanding-attribute-mapping-properties"></a>Förstå attribut-mappnings egenskaper

I föregående avsnitt har du redan lanserats till egenskaps typen attribut-mappning.
Tillsammans med den här egenskapen stöder attribut-mappningar även följande attribut:

- **Källattribut** – attributet användare från käll systemet (exempel: Azure Active Directory).
- **Target** -attribut – användarattribut i mål systemet (exempel: ServiceNow).
- **Matcha objekt med det här attributet** – om mappningen ska användas för att unikt identifiera användare mellan käll-och mål systemen. Den anges vanligt vis på userPrincipalName-eller e-postattributet i Azure AD, som vanligt vis mappas till ett användar namns fält i ett mål program.
- **Matchnings prioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som definieras av det här fältet. Så fort en matchning hittas utvärderas inga ytterligare matchande attribut.
- **Använd den här mappningen**
  - **Alltid** – Använd den här mappningen för både skapande av användare och uppdaterings åtgärder.
  - **Endast vid skapande** – Använd endast den här mappningen för åtgärder för att skapa användare.

## <a name="editing-group-attribute-mappings"></a>Redigera Gruppattribut – mappningar

Ett valt antal program, till exempel ServiceNow, Box och G Suite, stöder möjligheten att etablera grupp objekt och användar objekt. Grupp objekt kan innehålla grupp egenskaper, till exempel visnings namn och e-postalias, tillsammans med grupp medlemmar.

![Exempel visar ServiceNow med etablerade grupp-och användar objekt](./media/customize-application-attributes/24.png)

Grupp etablering kan aktive ras eller inaktive ras genom att du väljer grupp mappningen under **mappningar**och inställningen **aktive rad** till det alternativ som du vill använda på skärmen för **Mappning av attribut** .

De attribut som tillhandahålls som en del av grupp objekt kan anpassas på samma sätt som användar objekt, som beskrivs ovan. 

> [!TIP]
> Etablering av grupp objekt (egenskaper och medlemmar) är ett distinkt begrepp för att [tilldela grupper](assign-user-or-group-access-portal.md) till ett program. Det går att tilldela en grupp till ett program, men endast etablera de användar objekt som finns i gruppen. Det krävs ingen etablering av fullständiga grupp objekt för att använda grupper i tilldelningar.

## <a name="editing-the-list-of-supported-attributes"></a>Redigera listan över attribut som stöds

Användarattribut som stöds för ett angivet program är förkonfigurerade. De flesta programs API: er för användar hantering stöder inte schema identifiering. Azure AD Provisioning-tjänsten kan därför inte dynamiskt generera listan över attribut som stöds genom att ringa till programmet.

Vissa program stöder dock anpassade attribut och Azure AD Provisioning-tjänsten kan läsa och skriva till anpassade attribut. Om du vill ange deras definitioner i Azure Portal markerar du kryss rutan **Visa avancerade alternativ** längst ned på skärmen **Mappning av attribut** och väljer sedan **Redigera attributlistan för** din app.

Program och system som stöder anpassning av attributlistan är:

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory ([Azure AD Graph API standardattribut](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) och anpassade katalog tillägg stöds)
- Appar som stöder [SCIM 2,0](https://tools.ietf.org/html/rfc7643), där attribut som definierats i [kärn schemat](https://tools.ietf.org/html/rfc7643) måste läggas till

> [!NOTE]
> Redigering av listan över attribut som stöds rekommenderas endast för administratörer som har anpassat schemat för sina program och system, och som har första kunskap om hur deras egna attribut har definierats. Detta kräver ibland välbekanthet med de API: er och utvecklingsverktyg som tillhandahålls av ett program eller system.

När du redigerar listan över attribut som stöds anges följande egenskaper:

- **Namn** – system namnet för attributet, enligt definitionen i målobjektet.
- **Typ** – den typ av data som attributet lagrar, enligt definitionen i målobjektets schema, som kan vara någon av följande typer:
  - *Binary* -Attribute innehåller binära data.
  - *Boolean* -Attribute innehåller ett true-eller false-värde.
  - *Datetime* -attributet innehåller en datum sträng.
  - *Integer* -attributet innehåller ett heltal.
  - *Reference* -Attribute innehåller ett ID som refererar till ett värde som lagras i en annan tabell i mål programmet.
  - *String* -Attribute innehåller en text sträng.
- **Primär nyckel?** – Anger om attributet definieras som ett primär nyckel fält i målobjektets schema.
- **Kunna?** – Anger om attributet måste fyllas i mål programmet eller systemet.
- **Flera värden?** – Anger om attributet stöder flera värden.
- **Exakt fall?** – Om attributvärdena utvärderas på ett skift läges känsligt sätt.
- **API-uttryck** – Använd inte, om det inte instrueras att göra det i dokumentationen för en speciell etablerings anslutning (till exempel Workday).
- **Refererat objektattribut** – om det är ett attribut för referens typ kan du välja tabell och attribut i mål programmet som innehåller värdet som är kopplat till attributet. Om du till exempel har ett attribut med namnet "avdelning" vars lagrade värde refererar till ett objekt i en separat "avdelnings tabell" väljer du "Departments.Name". Referens tabellerna och de primära ID-fälten som stöds för ett angivet program är förkonfigurerade och kan för närvarande inte redige ras med hjälp av Azure Portal, men det går att redigera med hjälp av [Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Om du vill lägga till ett nytt attribut bläddrar du till slutet av listan över attribut som stöds, fyller i fälten ovan med de angivna indatana och väljer **Lägg till attribut**. Välj **Spara** när du är färdig med att lägga till attribut. Sedan måste du läsa in fliken **etablering** för de nya attributen så att de blir tillgängliga i redigeraren för attribut-mappning.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Återställa standardattribut och attribut mappningar

Om du behöver börja om och återställa dina befintliga mappningar till deras standard tillstånd, kan du markera kryss rutan **Återställ standard mappningar** och spara konfigurationen. Om du gör det anges alla mappningar som om programmet precis har lagts till i din Azure AD-klient från program galleriet.

Om du väljer det här alternativet tvingas en omsynkronisering av alla användare när etablerings tjänsten körs.

> [!IMPORTANT]
> Vi rekommenderar starkt att **etablerings status** är **inaktive rad** innan du anropar det här alternativet.

## <a name="what-you-should-know"></a>Det här bör du känna till

- Microsoft Azure AD ger en effektiv implementering av en synkroniseringsprocess. I en initierad miljö bearbetas endast objekt som kräver uppdateringar under en synkroniseringsprocess.
- Om du uppdaterar attribut-mappningar påverkas prestandan för en synkroniseringsprocess. En uppdatering av konfigurationen för attribut-mappning kräver att alla hanterade objekt utvärderas igen.
- En rekommenderad metod är att behålla antalet ändringar i följd av dina attribut-mappningar minst.
- Det går inte att lägga till ett foto attribut som ska tillhandahållas till en app idag eftersom du inte kan ange formatet för att synkronisera fotot. Du kan begära funktionen på [användarens röst](https://feedback.azure.com/forums/169401-azure-active-directory)
- Attributet IsSoftDeleted är ofta en del av standard mappningarna för ett program. IsSoftdeleted kan vara sant i ett av fyra scenarier (användaren är utanför omfånget eftersom den inte är tilldelad från programmet, användaren är utanför definitions området eftersom den inte uppfyller ett omfångs filter, användaren har varit mjuk borttagen i Azure AD, eller så har egenskapen AccountEnabled angetts till false  på användaren). 
- Azure AD Provisioning-tjänsten har inte stöd för etablering av null-värden

## <a name="next-steps"></a>Nästa steg

- [Automatisera användar etablering/avetablering för SaaS-appar](user-provisioning.md)
- [Skriva uttryck för attribut-mappningar](functions-for-customizing-application-data.md)
- [Omfångs filter för användar etablering](define-conditional-rules-for-provisioning-user-accounts.md)
- [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md)
- [Lista över självstudier om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)
