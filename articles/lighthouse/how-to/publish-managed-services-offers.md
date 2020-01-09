---
title: Publicera ett erbjudande för hanterade tjänster på Azure Marketplace
description: Lär dig hur du publicerar ett hanterat tjänst erbjudande som registrerar kunder till Azure-delegerad resurs hantering.
ms.date: 12/16/2019
ms.topic: conceptual
ms.openlocfilehash: d1eb06794551be498e05e2b9c3b893013b718ce9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453539"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Publicera ett erbjudande för hanterade tjänster på Azure Marketplace

I den här artikeln får du lära dig hur du publicerar ett offentligt eller privat hanterat tjänst erbjudande till [Azure Marketplace](https://azuremarketplace.microsoft.com) med hjälp av [Cloud Partner Portal](https://cloudpartner.azure.com/), vilket gör det möjligt för en kund som köper erbjudandet till onboard-resurser för Azure delegerad resurs hantering.

> [!NOTE]
> Du måste ha ett giltigt [konto i Partner Center](../../marketplace/partner-center-portal/create-account.md) för att kunna skapa och publicera dessa erbjudanden. Om du inte redan har ett konto kommer [registrerings processen](https://aka.ms/joinmarketplace) att leda dig genom stegen för att skapa ett konto i Partner Center och registrera dig i programmet för kommersiella marknads platser. Ditt Microsoft Partner Network (MPN) ID kommer [automatiskt att associeras](../../billing/billing-partner-admin-link-started.md) med de erbjudanden du publicerar för att spåra din påverkan på kund engagemang.
>
> Om du inte vill publicera ett erbjudande på Azure Marketplace kan du publicera kunder manuellt med hjälp av Azure Resource Manager mallar. Mer information finns i [publicera en kund till Azure delegerad resurs hantering](onboard-customer.md).

Att publicera ett erbjudande för hanterade tjänster liknar att publicera någon annan typ av erbjudande till Azure Marketplace. Mer information om den här processen finns i [Azure Marketplace och AppSource Publishing guide](../../marketplace/marketplace-publishers-guide.md) och [Hantera Azure-och AppSource Marketplace-erbjudanden](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md). Du bör också gå igenom de [kommersiella Marketplace-certifierings principerna](https://docs.microsoft.com/legal/marketplace/certification-policies), särskilt avsnittet [hanterade tjänster](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) .

När en kund lägger till erbjudandet kommer de att kunna delegera en eller flera specifika prenumerationer eller resurs grupper som sedan kommer att registreras [för Azure-delegerad resurs hantering](#the-customer-onboarding-process). Observera att innan en prenumeration (eller resurs grupper inom en prenumeration) kan registreras måste prenumerationen vara auktoriserad för onboarding genom att manuellt registrera **Microsoft. ManagedServices** -resurs leverantören.

> [!IMPORTANT]
> Varje plan i ett erbjudande för hanterade tjänster innehåller ett avsnitt om **manifest information** , där du definierar de Azure Active Directory (Azure AD) entiteter i din klient som ska ha åtkomst till de delegerade resurs grupperna och/eller prenumerationer för kunder som köper den planen. Det är viktigt att vara medveten om att alla grupper (eller användare eller tjänstens huvud namn) som du inkluderar har samma behörigheter för alla kunder som köper planen. Om du vill tilldela olika grupper för att arbeta med varje kund måste du publicera en separat privat plan som är exklusiv för varje kund.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Skapa ditt erbjudande i Cloud Partner Portal

1. Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. I den vänstra navigerings menyn väljer du **nytt erbjudande**och väljer sedan **hanterade tjänster**.
3. Du ser ett **redigerings** avsnitt för ditt erbjudande med fyra delar som du kan fylla i: **erbjudanden**, **planer**, **Marketplace**och **support**. Läs vidare för vägledning om hur du utför dessa avsnitt.

## <a name="enter-offer-settings"></a>Ange inställningar för erbjudandet

I avsnittet **erbjudande inställningar** anger du följande:

|Field  |Beskrivning  |
|---------|---------|
|**Erbjudande-ID**     | En unik identifierare för ditt erbjudande (i din utgivar profil). Detta ID får bara innehålla gemena alfanumeriska tecken, bindestreck och under streck, med högst 50 tecken. Tänk på att erbjudande-ID: t kan vara synligt för kunder på platser som i produkt-URL: er och fakturerings rapporter. När du har publicerat erbjudandet kan du inte ändra det här värdet.        |
|**Utgivar-ID**     | Utgivar-ID: t som ska associeras med erbjudandet. Om du har fler än ett utgivar-ID kan du välja det som du vill använda för det här erbjudandet.       |
|**Namn**     | Namnet (upp till 50 tecken) som kunderna kan se för ditt erbjudande på Azure Marketplace och i Azure Portal. Använd ett identifierbart varumärkes namn som kunderna förstår – om du befordrar det här erbjudandet via din egen webbplats måste du använda exakt samma namn här.        |

När du är klar väljer du **Spara**. Nu är du redo att gå vidare till avsnittet om **planer** .

## <a name="create-plans"></a>Skapa planer

Varje erbjudande måste ha en eller flera planer (kallas ibland SKU: er). Du kan lägga till flera planer för att stödja olika funktions uppsättningar i olika priser eller för att anpassa en viss plan för en begränsad mål grupp för vissa kunder. Kunder kan se de planer som är tillgängliga för dem under det överordnade erbjudandet.

I avsnittet planer väljer du **ny plan**. Ange sedan ett **plan-ID**. Detta ID får bara innehålla gemena alfanumeriska tecken, bindestreck och under streck, med högst 50 tecken. Plan-ID kan vara synligt för kunder på platser som i produkt-URL: er och fakturerings rapporter. När du har publicerat erbjudandet kan du inte ändra det här värdet.

### <a name="plan-details"></a>Plan information

Slutför följande avsnitt i avsnittet med **plan information** :

|Field  |Beskrivning  |
|---------|---------|
|**Rubrik**     | Eget namn på planen för visning. Maximal längd på 50 tecken.        |
|**Sammanfattning**     | Kortfattad beskrivning av planen för visning under rubriken. Maximal längd på 100 tecken.        |
|**Beskrivning**     | Beskrivnings text som ger en mer detaljerad förklaring av planen.         |
|**Faktureringsmodell**     | Det finns två fakturerings modeller som visas här, men du måste välja **Bring Your Own License** för Managed Services-erbjudanden. Det innebär att du debiteras kunderna direkt för kostnader som rör erbjudandet, och Microsoft debiterar inte några avgifter till dig.   |
|**Är det en privat plan?**     | Anger om SKU: n är privat eller offentlig. Standardvärdet är **no** (public). Om du lämnar det här alternativet begränsas inte din prenumeration till specifika kunder (eller till ett visst antal kunder). När du har publicerat en offentlig plan kan du inte senare ändra den till privat. Välj **Ja**om du vill att den här planen bara är tillgänglig för vissa kunder. När du gör det måste du identifiera kunderna genom att ange deras prenumerations-ID. De kan anges en i taget (för upp till 10 prenumerationer) eller genom att ladda upp en. csv-fil (för upp till 20 000 prenumerationer). Se till att ta med dina egna prenumerationer här så att du kan testa och validera erbjudandet. Mer information finns i [privata SKU: er och planer](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  |

> [!IMPORTANT]
> När en plan har publicerats som offentlig kan du inte ändra den till privat. Använd en privat plan för att kontrol lera vilka kunder som kan acceptera ditt erbjudande och delegera resurser. Med en offentlig plan kan du inte begränsa tillgänglighet till vissa kunder eller till och med ett visst antal kunder (även om du kan sluta sälja planen helt om du väljer att göra det). Det finns för närvarande ingen mekanism för att avvisa eller ta bort delegeringar när en kund accepterar ett erbjudande, men du kan alltid nå ut till en kund och be dem att [ta bort din åtkomst](view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="manifest-details"></a>Manifest information

Slutför ditt abonnemang genom att fylla i avsnittet **manifest information** . Detta skapar ett manifest med autentiseringsinformation för att hantera kund resurser. Den här informationen krävs för att aktivera Azure-delegerad resurs hantering.

> [!NOTE]
> Som anges ovan gäller användare och roller i dina **Authorization** -poster för alla kunder som köper planen. Om du vill begränsa åtkomsten till en specifik kund måste du publicera en privat plan för deras exklusiva användning.

Ange först en **version** för manifestet. Använd formatet *n. n. n* (till exempel 1.2.5).

Ange sedan **klient-ID: t**. Detta är ett GUID som är kopplat till organisationens Azure Active Directory-ID (dvs. den klient som du kommer att arbeta i för att hantera kundernas resurser). Om du inte har det här är praktiskt kan du hitta det genom att hovra över ditt konto namn längst upp till höger i Azure Portal eller genom att välja **Växla katalog**.

Slutligen lägger du till en eller flera **Authorization** -poster i planen. Auktoriseringar definierar de entiteter som har åtkomst till resurser och prenumerationer för kunder som köper planen och tilldelar roller som beviljar vissa åtkomst nivåer. Mer information om vilka roller som stöds finns i [innehavare, roller och användare i scenarier med Azure Lighthouse](../concepts/tenants-users-roles.md).

För varje **auktorisering**måste du ange följande. Du kan sedan välja **ny auktorisering** så många gånger som behövs för att lägga till fler användare och roll definitioner.

- **Azure AD-objekt-ID**: Azure AD-identifieraren för en användare, användar grupp eller ett program som kommer att beviljas vissa behörigheter (som beskrivs av roll definitionen) till dina kunders resurser.
- **Visnings namn för Azure AD-objekt**: ett eget namn som hjälper kunden att förstå syftet med den här auktoriseringen. Kunden ser det här namnet när resurser delegeras.
- **Roll definition**: Välj en av de tillgängliga inbyggda Azure AD-rollerna i listan. Den här rollen avgör de behörigheter som användaren i fältet **Azure AD-objekt-ID** kommer att ha på dina kunders resurser. Beskrivningar av dessa roller finns [inbyggda roller](../../role-based-access-control/built-in-roles.md) och [roll stöd för Azure-delegerad resurs hantering](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
- **Tilldelnings bara roller**: detta krävs endast om du har valt administratör för användar åtkomst i **roll definitionen** för den här auktoriseringen. I så fall måste du lägga till en eller flera tilldelnings bara roller här. Användaren i fältet **Azure AD-objekt-ID** kommer att kunna tilldela dessa tilldelnings bara **roller** till [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md), vilket krävs för att [distribuera principer som kan åtgärdas](deploy-policy-remediation.md). Observera att inga andra behörigheter som normalt är kopplade till rollen administratör för användar åtkomst gäller för den här användaren. Om du inte väljer en eller flera roller här skickas inte certifieringen. (Om du inte har valt administratör för användar åtkomst för den här användarens roll definition har det här fältet ingen inverkan.)

> [!TIP]
> I de flesta fall vill du tilldela behörigheter till en användar grupp eller ett tjänst huvud namn i Azure AD, i stället för till en serie med enskilda användar konton. På så sätt kan du lägga till eller ta bort åtkomst för enskilda användare utan att behöva uppdatera och publicera om planen när dina åtkomst krav ändras. Ytterligare rekommendationer finns i [klienter, roller och användare i Azure Lighthouse-scenarier](../concepts/tenants-users-roles.md).

När du har slutfört informationen kan du välja **ny plan** så många gånger du behöver för att skapa ytterligare planer. När du är klar väljer du **Spara**och fortsätter sedan till **Marketplace** -avsnittet.

## <a name="provide-marketplace-text-and-images"></a>Ange text och bilder i Marketplace

I avsnittet **Marketplace** kan du ange den text och de bilder som kunderna ser i Azure Marketplace och Azure Portal.

Fyll i följande fält i **översikts** avsnittet:

|Field  |Beskrivning  |
|---------|---------|
|**Rubrik**     |  Rubriken på erbjudandet, ofta det långa, formella namnet. Den här rubriken visas på ett framträdande sätt i Marketplace. Maximal längd på 50 tecken. I de flesta fall ska detta vara detsamma som det **namn** som du angav i avsnittet **erbjudande inställningar** .       |
|**Sammanfattning**     | Kort syfte eller funktion för ditt erbjudande. Detta visas vanligt vis under rubriken. Maximal längd på 100 tecken.        |
|**Lång Sammanfattning**     | En längre sammanfattning av ditt erbjudandes syfte eller funktion. Maximal längd på 256 tecken.        |
|**Beskrivning**     | Mer information om ditt erbjudande. Det här fältet innehåller den maximala längden på 3000 tecken och stöder enkel HTML-formatering. Du måste inkludera orden "hanterad tjänst" eller "hanterade tjänster" någonstans i beskrivningen.       |
|**Marknadsförings-ID**     | En unik identifierare för URL-vänlighet. Den här identifieraren får bara innehålla gemena alfanumeriska tecken och bindestreck. Den kommer att användas i Marketplace-URL: er för det här erbjudandet. Om ditt utgivar-ID t. ex. är *contoso* och din marknadsförings-ID är *fråga*, kommer URL: en för ditt erbjudande i Azure Marketplace att *https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp* .        |
|**Förhandsgranska prenumerations-ID: n**     | Lägg till en till 100 prenumerations-ID: n. De kunder som är kopplade till dessa prenumerationer kommer att kunna se erbjudandet på Azure Marketplace innan det går live. Vi rekommenderar att du inkluderar dina egna prenumerationer här så att du kan förhandsgranska hur ditt erbjudande visas på Azure Marketplace innan du gör det tillgängligt för kunderna.  (Microsoft support-och teknik team kommer också att kunna se ditt erbjudande under för hands perioden.)   |
|**Användbara länkar**     | URL: er som är relaterade till ditt erbjudande, till exempel dokumentation, viktig information, vanliga frågor och svar.        |
|**Föreslagna kategorier (max 5)**     | En eller flera kategorier (upp till fem) som gäller för ditt erbjudande. De här kategorierna hjälper kunder att upptäcka ditt erbjudande i Azure Marketplace och Azure Portal.        |

I avsnittet **marknadsförings artefakter** kan du ladda upp logo typer och andra till gångar som ska visas med ditt erbjudande. Du kan också ladda upp skärm bilder eller länkar till videor som hjälper kunderna att förstå ditt erbjudande.

Fyra logo typer krävs: **liten (40x40)** , **medium (90x90)** , **Large (115x115)** och **wide (255x115)** . Följ dessa rikt linjer för dina logo typer:

- Azure-designen har en enkel färgpalett. Begränsa antalet primära och sekundära färger i logotypen.
- Portalens temafärger är svart och vitt. Använd inte de färgerna som bakgrundsfärg för logotypen. Använd en färg som gör att logotyp framträder tydligt i portalen. Vi rekommenderar enkla primärfärger.
- Om du använder en genomskinlig bakgrund ser du till att logo typen och texten inte är vita, svarta eller blå.
- Logotypens utseende och känsla bör vara platt och inte innehålla toningar. Använd inte en tonad bakgrund för logotypen.
- Placera inte text på logotypen – inte ens företagets eller varumärkets namn.
- Se till att logotypen inte har sträckts ut.

**815x290-logotypen (hjälte)** är valfri men rekommenderas. Om du inkluderar en hjälte-logotyp följer du dessa rikt linjer:

- Ta inte med någon text i hjältens logo typ och se till att lämna 415 pixlar med tomt utrymme på höger sida av logo typen. Detta krävs för att lämna utrymme för text element som ska bäddas in program mässigt: ditt visnings namn för utgivare, plan rubrik, erbjudande lång Sammanfattning.
- Din hjältes logo typ bakgrund får inte vara svart, vit eller transparent. Kontrol lera att bakgrunds färgen inte är för ljus, eftersom den inbäddade texten kommer att visas i vitt.
- När du har publicerat erbjudandet med en hjälte-ikon kan du inte ta bort det (men du kan uppdatera det med en annan version om det behövs).

I avsnittet **ledar hantering** kan du välja det CRM-system där dina leads ska lagras. Observera att det krävs ett **lead-mål** enligt [certifierings principerna för hanterade tjänster](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services).

Ange slutligen din **sekretess policy URL** och **användningsvillkor** i avsnittet **juridiskt** . Du kan också ange om du vill använda [standard kontraktet](../../marketplace/standard-contract.md) för det här erbjudandet.

Se till att spara ändringarna innan du går vidare till avsnittet **support** .

## <a name="add-support-info"></a>Lägg till supportinformation

I avsnittet **support** anger du namn, e-postadress och telefonnummer för en teknisk kontakt och en kund support kontakt. Du måste också ange Support-URL: er. Microsoft kan använda den här informationen när vi behöver kontakta dig om affärs-och support ärenden.

När du har lagt till den här informationen väljer du **Spara.**

## <a name="publish-your-offer"></a>Publicera ditt erbjudande

När du har slutfört alla avsnitt är nästa steg att publicera erbjudandet på Azure Marketplace. Välj knappen **publicera** för att påbörja processen med att göra erbjudandet Live. Mer information om den här processen finns i [publicera Azure Marketplace-och AppSource-erbjudanden](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md).

Du kan när som helst [publicera en uppdaterad version av erbjudandet](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md) . Du kanske till exempel vill lägga till en ny roll definition till ett tidigare publicerat erbjudande. När du gör det, kommer kunder som redan har lagt till erbjudandet att se en ikon på sidan [**tjänst leverantörer**](view-manage-service-providers.md) i Azure Portal som gör att de kan se att en uppdatering är tillgänglig. Varje kund kan [granska ändringarna](view-manage-service-providers.md#update-service-provider-offers) och bestämma om de vill uppdatera till den nya versionen. 

## <a name="the-customer-onboarding-process"></a>Kundens onboarding-process

När en kund lägger till ditt erbjudande kan de [delegera en eller flera specifika prenumerationer eller resurs grupper](view-manage-service-providers.md#delegate-resources)som sedan kommer att registreras för Azure-delegerad resurs hantering. Om en kund har accepterat ett erbjudande men ännu inte har delegerat några resurser visas en anteckning längst upp i avsnittet **leverantörs erbjudanden** på sidan [**tjänst leverantörer**](view-manage-service-providers.md) i Azure Portal.

> [!IMPORTANT]
> Delegeringen måste utföras av ett konto som inte är gäst i kundens klient organisation som har den [inbyggda rollen ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) för den prenumeration som registreras (eller som innehåller de resurs grupper som har registrerats). Om du vill se alla användare som kan delegera prenumerationen kan en användare i kundens klient välja prenumerationen i Azure Portal, öppna **åtkomst kontroll (IAM)** , [lista alla roller](../../role-based-access-control/role-definitions-list.md#list-all-roles)och sedan välja **ägare** för att se alla användare med den rollen.

När kunden delegerar en prenumeration (eller en eller flera resurs grupper inom en prenumeration) registreras **Microsoft. ManagedServices** Resource Provider för den prenumerationen och användare i din klient organisation kan komma åt de delegerade resurserna enligt auktoriseringarna i ditt erbjudande.

> [!NOTE]
> För närvarande kan inte prenumerationer (eller resurs grupper inom en prenumeration) delegeras om prenumerationen använder Azure Databricks. Om en prenumeration (eller resurs grupper i en prenumeration) redan har delegerats är det för närvarande inte möjligt att skapa Databricks-arbetsytor i den prenumerationen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterings upplevelser mellan flera innehavare](../concepts/cross-tenant-management-experience.md).
- [Visa och hantera kunder](view-manage-customers.md) genom att gå till **mina kunder** i Azure Portal.
