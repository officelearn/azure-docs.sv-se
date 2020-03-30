---
title: Publicera erbjudande om hanterade tjänster på Azure Marketplace
description: Lär dig hur du publicerar ett hanterat tjänsterbjudande som ombordar kunder till Azure-delegerad resurshantering.
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 6ae93759073be6b05d118ccf46f6b6367fff5fc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328950"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Publicera erbjudande om hanterade tjänster på Azure Marketplace

I den här artikeln får du lära dig hur du publicerar ett erbjudande om offentliga eller privata hanterade tjänster till [Azure Marketplace](https://azuremarketplace.microsoft.com) med hjälp av Cloud [Partner Portal](https://cloudpartner.azure.com/), vilket gör det möjligt för en kund som köper erbjudandet till inbyggda resurser för Azure-delegerad resurshantering.

> [!NOTE]
> Du måste ha ett giltigt [konto i Partner Center](../../marketplace/partner-center-portal/create-account.md) för att kunna skapa och publicera dessa erbjudanden. Om du inte redan har ett konto leder [registreringsprocessen](https://aka.ms/joinmarketplace) dig genom stegen för att skapa ett konto i Partner Center och registrera dig i programmet Commercial Marketplace. Mpn-ID-nätverket (Microsoft Partner Network) [kopplas automatiskt till](../../billing/billing-partner-admin-link-started.md) de erbjudanden du publicerar för att spåra din påverkan på kundengagemang.
>
> Om du inte vill publicera ett erbjudande till Azure Marketplace kan du registrera kunder manuellt med hjälp av Azure Resource Manager-mallar. Mer information finns [i Ombord på en kund till Azure-delegerad resurshantering](onboard-customer.md).

Att publicera ett erbjudande om hanterade tjänster liknar att publicera alla andra typer av erbjudanden till Azure Marketplace. Mer information om den processen finns i [Azure Marketplace och AppSource Publishing Guide](../../marketplace/marketplace-publishers-guide.md) och Hantera [Azure- och AppSource Marketplace-erbjudanden](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md). Du bör också granska [certifieringsprinciperna för kommersiella marknadsplatser,](https://docs.microsoft.com/legal/marketplace/certification-policies)särskilt avsnittet [Managed Services.](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)

När en kund lägger till ditt erbjudande kan de delegera en eller flera specifika prenumerationer eller resursgrupper som sedan finns [ombord för Azure-delegerad resurshantering](#the-customer-onboarding-process). Observera att innan en prenumeration (eller resursgrupper inom en prenumeration) kan vara ombord måste prenumerationen auktoriseras för introduktion genom att manuellt registrera **microsoft.ManagedServices** resursprovider.

> [!IMPORTANT]
> Varje plan i ett erbjudande om hanterade tjänster innehåller ett **manifestinformationsavsnitt,** där du definierar Azure Active Directory (Azure AD) entiteterna i din klientorganisation som kommer att ha åtkomst till de delegerade resursgrupperna och/eller prenumerationerna för kunder som köper den planen. Det är viktigt att vara medveten om att alla grupper (eller användar- eller tjänsthuvudnamn) som du inkluderar här har samma behörighet för varje kund som köper planen. Om du vill tilldela olika grupper att arbeta med varje kund måste du publicera en separat privat plan som är exklusiv för varje kund.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Skapa ditt erbjudande i Cloud Partner Portal

1. Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).
2. Välj **Nytt erbjudande**på menyn till vänster och välj sedan **Hanterade tjänster**.
3. Du ser ett **editoravsnitt** för ditt erbjudande med fyra delar att fylla i: **Erbjudandeinställningar,** **Abonnemang,** **Marknadsplats**och **Support**. Läs vidare för vägledning om hur du fyller i dessa avsnitt.

## <a name="enter-offer-settings"></a>Ange erbjudandeinställningar

Ange följande i avsnittet **Erbjudandeinställningar:**

|Field  |Beskrivning  |
|---------|---------|
|**Erbjudande-ID**     | En unik identifierare för ditt erbjudande (i din utgivarprofil). Det här ID:t kan bara innehålla gemener alfanumeriska tecken, streck och understreck med högst 50 tecken. Tänk på att erbjudande-ID:t kan vara synligt för kunder på platser som i produktadresser och faktureringsrapporter. När du har publicerat erbjudandet kan du inte ändra det här värdet.        |
|**Publisher-ID**     | Utgivar-ID:et som ska kopplas till erbjudandet. Om du har mer än ett utgivar-ID kan du välja det du vill använda för det här erbjudandet.       |
|**Namn**     | Namnet (upp till 50 tecken) som kunderna ser för ditt erbjudande på Azure Marketplace och i Azure-portalen. Använd ett igenkännbart varumärke som kunderna förstår – om du marknadsför det här erbjudandet via din egen webbplats måste du använda exakt samma namn här.        |

När du är klar väljer du **Spara**. Nu är du redo att gå vidare till avsnittet **Planer.**

## <a name="create-plans"></a>Skapa planer

Varje erbjudande måste ha en eller flera planer (kallas ibland SKU:er). Du kan lägga till flera abonnemang för att stödja olika funktionsuppsättningar till olika priser eller för att anpassa en viss plan för en begränsad målgrupp av specifika kunder. Kunder kan visa de planer som är tillgängliga för dem under det överordnade erbjudandet.

Välj **Ny plan**i avsnittet Planer . Ange sedan ett **plan-ID**. Det här ID:t kan bara innehålla gemener alfanumeriska tecken, streck och understreck med högst 50 tecken. Plan-ID:t kan vara synligt för kunder på platser som i produktadresser och faktureringsrapporter. När du har publicerat erbjudandet kan du inte ändra det här värdet.

### <a name="plan-details"></a>Planera detaljer

Fyll i följande avsnitt i avsnittet **Planera information:**

|Field  |Beskrivning  |
|---------|---------|
|**Titel**     | Eget namn för planen för visning. Maximal längd på 50 tecken.        |
|**Sammanfattning**     | Kortfattad beskrivning av planen för visning under rubriken. Maximal längd på 100 tecken.        |
|**Beskrivning**     | Beskrivningstext som ger en mer detaljerad förklaring av planen.         |
|**Faktureringsmodell**     | Det finns 2 faktureringsmodeller som visas här, men du måste välja **Ta med din egen licens** för hanterade tjänster erbjudanden. Det innebär att du debiterar dina kunder direkt för kostnader i samband med detta erbjudande, och Microsoft tar inte ut några avgifter till dig.   |
|**Är det här en privat plan?**     | Anger om SKU:n är privat eller offentlig. Standardvärdet är **Nej** (offentligt). Om du lämnar det här valet begränsas inte din plan till specifika kunder (eller till ett visst antal kunder). När du har publicerat en offentlig plan kan du inte senare ändra den till privat. Om du bara vill göra den här planen tillgänglig för specifika kunder väljer du **Ja**. När du gör det måste du identifiera kunderna genom att ange deras prenumerations-ID. Dessa kan anges en efter en (för upp till 10 prenumerationer) eller genom att ladda upp en CSV-fil (för upp till 20 000 prenumerationer). Var noga med att inkludera dina egna prenumerationer här så att du kan testa och validera erbjudandet. Mer information finns i [Privata SKU:er och planer](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  |

> [!IMPORTANT]
> När en plan har publicerats som offentlig kan du inte ändra den till privat. Om du vill styra vilka kunder som kan acceptera erbjudandet och delegera resurser använder du en privat plan. Med en offentlig plan kan du inte begränsa tillgängligheten till vissa kunder eller ens till ett visst antal kunder (även om du kan sluta sälja planen helt om du väljer att göra det). Du kan [ta bort åtkomsten till en delegering](onboard-customer.md#remove-access-to-a-delegation) när en kund bara har accepterat ett erbjudande om du har inkluderat ett **auktorisering med** **rolldefinitionen** inställd på [Borttagning av tilldelning](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) av hanterade tjänster när du publicerade erbjudandet. Du kan också nå ut till kunden och be dem att [ta bort din åtkomst](view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="manifest-details"></a>Manifestdetaljer

Fyll i avsnittet **Manifestinformation** för din plan. Detta skapar ett manifest med auktoriseringsinformation för hantering av kundresurser. Den här informationen krävs för att aktivera Azure-delegerad resurshantering.

> [!NOTE]
> Som nämnts ovan gäller användare och roller i **dina auktoriseringstransaktioner** för varje kund som köper planen. Om du vill begränsa åtkomsten till en viss kund måste du publicera en privat plan för deras exklusiva användning.

Ange först en **version** för manifestet. Använd formatet *n.n.n* (till exempel 1.2.5).

Ange sedan ditt **klient-ID**. Detta är ett GUID som är associerat med Azure Active Directory-klient-ID för din organisation (dvs. klienten som du kommer att arbeta med för att hantera dina kunders resurser). Om du inte har det här praktiskt kan du hitta det genom att hovra över ditt kontonamn på den övre högra sidan av Azure-portalen, eller genom att välja **Växla katalog**.

Lägg slutligen till en eller **flera auktoriseringsposter** i planen. Auktoriseringar definierar de entiteter som kan komma åt resurser och prenumerationer för kunder som köper planen och tilldela roller som ger specifika åtkomstnivåer.

> [!TIP]
> I de flesta fall vill du tilldela behörigheter till en Azure AD-användargrupp eller tjänsthuvudnamn, i stället för till en serie enskilda användarkonton. På så sätt kan du lägga till eller ta bort åtkomst för enskilda användare utan att behöva uppdatera och publicera om planen när åtkomstkraven ändras. Ytterligare rekommendationer finns i [Klienter, roller och användare i Azure Lighthouse-scenarier](../concepts/tenants-users-roles.md).

För varje **auktorisering**måste du ange följande. Du kan sedan välja **Ny auktorisering** så många gånger som behövs för att lägga till fler användare och rolldefinitioner.

- **Azure AD-objekt-ID:** Azure AD-identifieraren för en användare, användargrupp eller ett program som kommer att beviljas vissa behörigheter (enligt beskrivningen av rolldefinitionen) till dina kunders resurser.
- **Visningsnamn för Azure AD-objekt:** Ett eget namn som hjälper kunden att förstå syftet med den här auktoriseringen. Kunden ser det här namnet när du delegerar resurser.
- **Rolldefinition**: Välj en av de tillgängliga Azure AD-inbyggda rollerna i listan. Den här rollen avgör vilka behörigheter som användaren i fältet **Azure AD Object ID** ska ha på dina kunders resurser. Beskrivningar av dessa roller finns i Inbyggda roller och [rollstöd för Azure-delegerad resurshantering](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management). [Built-in roles](../../role-based-access-control/built-in-roles.md)
  > [!NOTE]
  > När nya inbyggda roller läggs till i Azure blir de tillgängliga här, även om det kan finnas vissa fördröjningar innan de visas.
- **Tilldelningsbara roller**: Detta krävs bara om du har valt Användaråtkomstadministratör i **rolldefinitionen** för den här auktoriseringen. I så fall måste du lägga till en eller flera tilldelningsbara roller här. Användaren i fältet **Azure AD Object ID** kan tilldela dessa **assignable Roles** till hanterade [identiteter](../../active-directory/managed-identities-azure-resources/overview.md), vilket krävs för att [distribuera principer som kan åtgärdas](deploy-policy-remediation.md). Observera att inga andra behörigheter som normalt associeras med rollen Administratör för användaråtkomst gäller för den här användaren. Om du inte väljer en eller flera roller här kommer ditt bidrag inte att certifieras. (Om du inte valde User Access Administrator för den här användarens rolldefinition har det här fältet ingen effekt.)

> [!TIP]
> Om du vill vara säkra på att du kan [ta bort åtkomsten till en delegering](onboard-customer.md#remove-access-to-a-delegation) om det behövs tar du med en **auktorisering** med **rolldefinitionen** som angetts till [Rollen Borttagning av tilldelning](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)av hanterade tjänster . Om den här rollen inte tilldelas kan delegerade resurser endast tas bort av en användare i kundens klientorganisation.

När du har slutfört informationen kan du välja **Ny plan** så många gånger som du behöver för att skapa ytterligare planer. När du är klar väljer du **Spara**och fortsätter sedan till avsnittet **Marketplace.**

## <a name="provide-marketplace-text-and-images"></a>Tillhandahålla Marketplace-text och -bilder

Avsnittet **Marketplace** är där du tillhandahåller text och avbildningar som kunderna ser i Azure Marketplace och Azure-portalen.

Fyll i följande fält i avsnittet **Översikt:**

|Field  |Beskrivning  |
|---------|---------|
|**Titel**     |  Erbjudandets titel, ofta det långa, formella namnet. Den här titeln visas tydligt på marknaden. Maximal längd på 50 tecken. I de flesta fall bör detta vara samma som det **namn** som du angav i avsnittet **Erbjudandeinställningar.**       |
|**Sammanfattning**     | Kort syfte eller funktion av ditt erbjudande. Detta visas vanligtvis under titeln. Maximal längd på 100 tecken.        |
|**Lång sammanfattning**     | En längre sammanfattning av syftet eller funktionen för ditt erbjudande. Maximal längd på 256 tecken.        |
|**Beskrivning**     | Mer information om ditt erbjudande. Det här fältet har en maximal längd på 3 000 tecken och stöder enkel HTML-formatering. Du måste inkludera orden "hanterad tjänst" eller "hanterade tjänster" någonstans i beskrivningen.       |
|**Marknadsidentifierare**     | En unik URL-vänlig identifierare. Den här identifieraren kan bara innehålla gemener alfanumeriska tecken och streck. Den kommer att användas i Marketplace-URL:er för det här erbjudandet. Om ditt utgivar-ID till exempel är *contoso* och din marknadsföringsidentifierare *https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp*är *sampleApp,* kommer url:en för ditt erbjudande på Azure Marketplace att vara .        |
|**Förhandsgranska prenumerations-ID:er**     | Lägg till en till 100 prenumerationsidentifierare. De kunder som är associerade med dessa prenumerationer kan visa erbjudandet på Azure Marketplace innan det publiceras. Vi föreslår att du inkluderar dina egna prenumerationer här så att du kan förhandsgranska hur ditt erbjudande visas på Azure Marketplace innan du gör det tillgängligt för kunder.  (Microsofts support- och teknikteam kan också se erbjudandet under den här förhandsgranskningsperioden.)   |
|**Användbara länkar**     | Webbadresser som är relaterade till ditt erbjudande, till exempel dokumentation, viktig information, vanliga frågor och svar osv.        |
|**Föreslagna kategorier (max 5)**     | En eller flera kategorier (upp till fem) som gäller för ditt erbjudande. Dessa kategorier hjälper kunderna att upptäcka ditt erbjudande i Azure Marketplace och Azure-portalen.        |

I avsnittet **Marknadsföringsartefakter** kan du ladda upp logotyper och andra resurser som ska visas med ditt erbjudande. Du kan också ladda upp skärmdumpar eller länkar till videor som kan hjälpa kunderna att förstå ditt erbjudande.

Fyra logotypstorlekar krävs: **Små (40x40),** **Medium (90x90),** **Stor (115x115)** och **Bred (255x115)**. Följ dessa riktlinjer för dina logotyper:

- Azure-designen har en enkel färgpalett. Begränsa antalet primära och sekundära färger i logotypen.
- Portalens temafärger är svart och vitt. Använd inte de färgerna som bakgrundsfärg för logotypen. Använd en färg som gör att logotyp framträder tydligt i portalen. Vi rekommenderar enkla primärfärger.
- Om du använder en genomskinlig bakgrund måste du se till att logotypen och texten inte är vit, svart eller blå.
- Logotypens utseende och känsla bör vara platt och inte innehålla toningar. Använd inte en tonad bakgrund för logotypen.
- Placera inte text på logotypen – inte ens företagets eller varumärkets namn.
- Se till att logotypen inte har sträckts ut.

Hero **-logotypen (815x290)** är valfritt men rekommenderas. Om du inkluderar en hjältelogotyp följer du dessa riktlinjer:

- Ta inte med någon text i hjältelogotypen och se till att lämna 415 pixlar tomt utrymme till höger om logotypen. Detta krävs för att lämna utrymme för textelement som kommer att bäddas in programmässigt: din utgivare visa namn, plan titel, erbjuder lång sammanfattning.
- Hjältelogotypens bakgrund kanske inte är svart, vit eller genomskinlig. Kontrollera att bakgrundsfärgen inte är för ljus eftersom den inbäddade texten visas i vitt.
- När du har publicerat erbjudandet med en hjälteikon kan du inte ta bort det (även om du kan uppdatera det med en annan version om så önskas).

I avsnittet **Lead Management** kan du välja det CRM-system där dina leads ska lagras. Observera att enligt [certifieringsprinciperna för hanterade tjänster](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)krävs ett **leadmål.**

Ange slutligen **webbadressen till sekretesspolicyn** och **användarvillkoren** i avsnittet **Juridiska frågor.** Du kan också ange här om [standardkontraktet](../../marketplace/standard-contract.md) för det här erbjudandet ska användas eller inte.

Var noga med att spara ändringarna innan du går vidare till avsnittet **Support.**

## <a name="add-support-info"></a>Lägga till supportinformation

Ange namn, e-postadress och telefonnummer för en teknisk kontakt och en kundsupportkontakt i avsnittet **Support.** Du måste också ange supportadresser. Microsoft kan använda den här informationen när vi behöver kontakta dig om affärs- och supportproblem.

När du har lagt till den här informationen väljer du **Spara.**

## <a name="publish-your-offer"></a>Publicera ditt erbjudande

När du har slutfört alla avsnitt är nästa steg att publicera erbjudandet till Azure Marketplace. Välj knappen **Publicera** för att starta processen med att göra erbjudandet live. Mer information om den här processen finns i [Publicera Azure Marketplace- och AppSource-erbjudanden](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md).

Du kan när som helst [publicera en uppdaterad version av erbjudandet.](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md) Du kanske till exempel vill lägga till en ny rolldefinition i ett tidigare publicerat erbjudande. När du gör det ser kunder som redan har lagt till erbjudandet en ikon på sidan [**Tjänstleverantörer**](view-manage-service-providers.md) i Azure-portalen som låter dem veta att en uppdatering är tillgänglig. Varje kund kommer att kunna [granska ändringarna](view-manage-service-providers.md#update-service-provider-offers) och bestämma om de vill uppdatera till den nya versionen. 

## <a name="the-customer-onboarding-process"></a>Kundens introduktionsprocess

När en kund har lägger till ditt erbjudande kan de [delegera en eller flera specifika prenumerationer eller resursgrupper](view-manage-service-providers.md#delegate-resources), som sedan kommer att finnas ombord för Azure-delegerad resurshantering. Om en kund har accepterat ett erbjudande men ännu inte har delegerat några resurser visas en anteckning högst upp på avsnittet **Provider-erbjudanden** på sidan [**Tjänstleverantörer**](view-manage-service-providers.md) i Azure-portalen.

> [!IMPORTANT]
> Delegering måste göras av ett icke-gästkonto i kundens klientorganisation som har [rollen Ägare inbyggt](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) för prenumerationen som är ombord (eller som innehåller de resursgrupper som är inbyggda). Om du vill visa alla användare som kan delegera prenumerationen kan en användare i kundens klientorganisation välja prenumerationen i Azure-portalen, öppna **åtkomstkontroll (IAM)** och [visa alla användare med ägarrollen](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

När kunden har delegerat en prenumeration (eller en eller flera resursgrupper inom en prenumeration) registreras resursleverantören **Microsoft.ManagedServices** för den prenumerationen och användare i din klientorganisation kan komma åt de delegerade resurserna enligt auktoriseringarna i erbjudandet.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanteringsupplevelser mellan klienter.](../concepts/cross-tenant-management-experience.md)
- [Visa och hantera kunder](view-manage-customers.md) genom att gå till **Mina kunder** i Azure-portalen.
