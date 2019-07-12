---
title: Publicera ett erbjudande med hanterade tjänster på Azure Marketplace
description: Lär dig hur du publicerar en hanterad tjänst-erbjudande som registrerar virtuella kunderna till Azure delegerad resurshantering.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: bb2f26a170bbd60eb927bd00f6def7d033fafee9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809850"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Publicera ett erbjudande med hanterade tjänster på Azure Marketplace

I den här artikeln du lär dig hur du publicerar ett erbjudande för offentliga eller privata hanterade tjänster till [Azure Marketplace](https://azuremarketplace.microsoft.com) med hjälp av den [Cloud Partner Portal](https://cloudpartner.azure.com/), aktiverar en kund som köper erbjudanden som ska publiceras delegerad resurshantering för Azure. 

> [!NOTE]
> Du måste ha ett giltigt [konto Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) skapa och publicera dessa erbjudanden. Om du inte redan har ett konto i [registreringsprocessen](https://aka.ms/joinmarketplace) leder dig igenom stegen för att skapa ett konto i Partnercenter och registrera i kommersiella Marketplace-program. Ditt Microsoft Partner Network (MPN)-ID ska vara [automatiskt associerade](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) med erbjudanden som du publicerar om du vill spåra din inverkan i arbetet med kunder.
>
> Om du inte vill publicera ett erbjudande på Azure Marketplace kan du registrera kunder manuellt med hjälp av Azure Resource Manager-mallar. Mer information finns i [publicerat en kund till Azure delegerad resurshantering](onboard-customer.md).

Publicering av hanterade tjänster liknar erbjudandet publicera någon annan typ av erbjudande på Azure Marketplace. Mer information om den här processen, se [Azure Marketplace och AppSource publiceringsguide](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) och [hantera Azure och AppSource-marknadsplatsen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

> [!IMPORTANT]
> Varje plan i en hanterade tjänster-erbjudandet innehåller en **Manifest information** avsnitt, där du definierar Azure Active Directory (Azure AD)-entiteter i din klient som har åtkomst till den delegerade resursgrupper och/eller prenumerationer för kunder som köper den här planen. Det är viktigt att Observera att alla grupp (eller användaren eller tjänstens huvudnamn) som inkluderas här har samma behörigheter för alla kunder som köper planen. Om du vill tilldela olika grupper att arbeta med varje kund, måste du publicera en separat privat plan som är exklusivt för varje kund.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Skapa ditt erbjudande i partnerportalen i molnet

1. Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Den vänstra menyn väljer du **nytt erbjudande**och välj sedan **-hanterade tjänsterna**.
3. Du ser en **redigeraren** erbjudandet med fyra delar att fylla i avsnittet: **Erbjudandeinställningar**, **planer**, **Marketplace**, och **Support**. Läs vidare anvisningar om hur du utför dessa avsnitt.

## <a name="enter-offer-settings"></a>Ange inställningar för erbjudandet

I den **Erbjudandeinställningar** och ange följande:

|Fält  |Beskrivning  |
|---------|---------|
|**Erbjudande-ID**     | En unik identifierare för ditt erbjudande (inom din publisher-profil). Detta ID kan bara innehålla gemena alfanumeriska tecken, bindestreck och understreck, med högst 50 tecken. Tänk på att erbjudande-ID kan vara synliga för kunderna på platser som i URL: er i produkten och fakturering rapporter. När du publicera erbjudandet kan du inte ändra det här värdet.        |
|**Utgivar-ID**     | Publicerings-ID som ska associeras med erbjudandet. Om du har mer än en publicerings-ID, kan du välja den du vill använda för det här erbjudandet.       |
|**Namn**     | Namnet (upp till 50 tecken) som kommer att visas för ditt erbjudande i Azure Marketplace och i Azure-portalen. Använd ett beskrivande namn, varumärke, som kunder kan förstå – om du uppgradera det här erbjudandet via din egen webbplats, kan du använda exakt samma namn här.        |

När du är klar, väljer **spara**. Nu är du redo att gå vidare till den **planer** avsnittet.

## <a name="create-plans"></a>Skapa planer

Varje erbjudande måste ha en eller flera planer (kallas ibland SKU: er). Du kan lägga till flera planer för att stödja olika funktionsuppsättningar till olika priser eller anpassa en specifik plan för en begränsad publik för specifika kunder. Kunder kan se de planer som är tillgängliga i det överordnade-erbjudandet.

I avsnittet planer för varje prenumeration som du vill skapa, Välj **ny Plan**. Ange sedan en **Serviceplans-ID:** . Detta ID kan bara innehålla gemena alfanumeriska tecken, bindestreck och understreck, med högst 50 tecken. Plan-ID: T kan vara synliga för kunderna på platser som i URL: er i produkten och fakturering rapporter. När du publicera erbjudandet kan du inte ändra det här värdet.

Sedan Slutför avsnitten i den **planera information** avsnittet:

|Fält  |Beskrivning  |
|---------|---------|
|**Rubrik**     | Eget namn för schemat för visning. Högst 50 tecken.        |
|**Sammanfattning**     | Kortfattad beskrivning av planen för visning under rubriken. Högst 100 tecken.        |
|**Beskrivning**     | Beskrivningstext som ger en mer detaljerad förklaring av planen.         |
|**Faktureringsmodell**     | Det finns 2 faktureringsmodellerna som visas här, men du måste välja **använda din egen licens** för hanterade tjänster-erbjudanden. Det innebär att du fakturerar dina kunder direkt för kostnaderna för det här erbjudandet och debiteras inte Microsoft eventuella avgifter för dig.   |
|**Är detta en privat Plan?**     | Anger om SKU: N är privat eller offentlig. Standardvärdet är **nr** (offentliga). Om du lämnar det här alternativet kan kan din plan inte begränsat till specifika kunder (eller till ett visst antal kunder); När du har publicerat en offentlig plan ändra du inte senare den till privat. Gör den här planen tillgänglig endast för specifika kunder, markera **Ja**. När du gör det behöver du identifiera kunderna genom att tillhandahålla sina prenumerations-ID. Det kan vara anges en i taget (för upp till 10 prenumerationer) eller genom att ladda upp en .csv-fil (för upp till 20 000 prenumerationer). Var noga med att inkludera egna prenumerationer här så att du kan testa och validera erbjudandet. Mer information finns i [privat SKU: er och planer](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  |

Slutför den **Manifest information** avsnittet. Detta skapar ett manifest med autentiseringsinformation för att hantera kundresurser. Den information som du anger här är nödvändigt att publicera dina kunder för Azure delegerad resurshantering. Ovanstående gäller dessa behörigheter för alla kunder som köper planen, så om du vill begränsa åtkomsten till en viss kund, du måste publicera en privat plan för eget bruk.

- Börja med att ange en **Version** för manifestet. Använd formatet *n.n.n* (till exempel 1.2.5).
- Ange sedan ditt **klient-ID**. Det här är ett GUID som är associerade med Azure Active Directory-klient-ID för din organisation (d.v.s. den klient som du kommer att arbeta i att hantera dina kunders resurser). Om du inte har det här praktiska, kan du hitta den genom att hovra över namnet på ditt konto längst upp till höger i Azure portal eller genom att välja **växla katalog**. 
- Slutligen lägger du till en eller flera **auktorisering** poster för din prenumeration. Auktoriseringar definierar de entiteter som har åtkomst till resurser och prenumerationer för kunder som köper planen. Du måste ange den här informationen för att komma åt resurser åt kunden med hjälp av Azure delegerade resource Manager.
  Ange följande för varje tillstånd. Du kan välja **nya auktorisering** så många gånger som behövs för att lägga till fler användare/rolldefinitioner.
  - **Azure AD-objekt-ID**: Azure AD-ID för en användare, grupp eller program som kommer att beviljas särskilda behörigheter (som beskrivs av rolldefinitionen) till dina kunders resurser.
  - **Azure AD-objektets visningsnamn**: Ett eget namn som hjälper kunden förstå syftet med den här auktoriseringen. Kunden ser det här namnet när du delegerar resurser.
  - **Rolldefinition**: Välj en av de tillgängliga inbyggda Azure AD-roller i listan. Den här rollen avgör behörigheterna som användaren i den **Azure AD-objekt-ID** fält kommer att ha på kundernas resurser. Information om de här rollerna finns i [inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  - **Tilldelningsbara roller**: Om du har valt administratör för användaråtkomst i den **rolldefinition** för detta tillstånd kan du lägga till en eller flera tilldelningsbara roller här. Användare i den **Azure AD-objekt-ID** fält kommer att kunna tilldela dem **Tilldelningsbara roller** till [hanterade identiteter](https://docs.microsoft.com/azure/managed-applications/publish-managed-identity). Observera att inga andra behörigheter som normalt förknippas med rollen Administratör för användaråtkomst gäller för den här användaren. (Om du inte valde administratör för användaråtkomst för den här användaren rolldefinition, det här fältet har ingen effekt.)

> [!TIP]
> I de flesta fall är det bäst att tilldela behörigheter till en Azure AD-användargruppen eller tjänstens huvudnamn, snarare än vid ett antal enskilda användarkonton. På så sätt kan du lägga till eller ta bort åtkomst för enskilda användare utan att behöva uppdatera och publicera planen när ändrar dina krav för åtkomst.

När du är klar att lägga till planer, Välj **spara**, fortsätt sedan till den **Marketplace** avsnittet.

## <a name="provide-marketplace-text-and-images"></a>Ange Marketplace text och bilder

Den **Marketplace** avsnittet kan du ge text och bilder som kommer att se i Azure Marketplace och Azure-portalen.

Ange information för följande fält i den **översikt** avsnittet:

|Fält  |Beskrivning  |
|---------|---------|
|**Rubrik**     |  Rubrik för erbjudandet, ofta lång, formella namn. Den här rubriken visas på en framträdande plats i marketplace. Högst 50 tecken. I de flesta fall bör detta vara samma som den **namn** du angav i den **erbjuder inställningar** avsnittet.       |
|**Sammanfattning**     | Kort syfte eller funktion i ditt erbjudande. Detta är vanligtvis visas under rubriken. Högst 100 tecken.        |
|**Lång sammanfattning**     | En längre sammanfattning av syfte eller funktion i ditt erbjudande. Högst 256 tecken.        |
|**Beskrivning**     | Mer information om ditt erbjudande. Det här fältet har en maximal längd på 3000 tecken och har stöd för enkel HTML-formatering.        |
|**Identifierare för marknadsföring**     | En unik identifierare för URL-vänlig. det ska användas i Marketplace URL: er för det här erbjudandet. Exempel: om Publicerings-ID är *contoso* och din marknadsföring identifieraren är *exempelapp för*, URL: en för ditt erbjudande i Azure Marketplace blir *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* .        |
|**Förhandsversion av prenumerations-ID: N**     | Lägg till en till 100 identifierare för prenumerationen. Kunder som är associerade med dessa prenumerationer kommer att kunna visa erbjudandet på Azure Marketplace innan det lanseras. Vi rekommenderar, inklusive dina egna prenumerationer här så att du kan förhandsgranska hur ditt erbjudande ska visas i Azure Marketplace innan du gör den tillgänglig för kunder.  (Microsoft-supporten och tekniska team kommer även att kunna visa ditt erbjudande under denna förhandsgranskningsperiod.)   |
|**Användbara länkar**     | URL: er som rör ditt erbjudande, till exempel dokumentation, viktig information, vanliga frågor och svar, osv.        |
|**Föreslås kategorier (Max 5)**     | En eller flera kategorier (upp till fem) som gäller för ditt erbjudande. Dessa kategorier hjälpa kunderna att identifiera ditt erbjudande i Azure Marketplace och Azure-portalen.        |

I den **marknadsföring artefakter** avsnittet, kan du överföra logotyper och andra resurser som ska visas i ditt erbjudande. Du kan också ladda upp skärmbilder eller länkar till videor som hjälper kunder att förstå ditt erbjudande.

Det krävs fyra logotyp storlekar: **Liten (40 x 40)** , **medel (90 x 90)** , **stora (115 x 115)** , och **hela (255 x 155)** . Följ dessa riktlinjer för din logotyper:

- Azure-designen har en enkel färgpalett. Begränsa antalet primära och sekundära färger i logotypen.
- Portalens temafärger är svart och vitt. Använd inte de färgerna som bakgrundsfärg för logotypen. Använd en färg som gör att logotyp framträder tydligt i portalen. Vi rekommenderar enkla primärfärger.
- Om du använder en genomskinlig bakgrund, se till att logotypen och texten inte vitt, svart eller blå.
- Logotypens utseende och känsla bör vara platt och inte innehålla toningar. Använd inte en tonad bakgrund för logotypen.
- Placera inte text på logotypen – inte ens företagets eller varumärkets namn.
- Se till att logotypen inte har sträckts ut.

Den **Hero (815 x 290)** logotyp är valfritt men rekommenderas. Om du inkluderar en hero-logotyp, Följ dessa riktlinjer:

- Inte inkluderar text till hero-logotypen och se till att lämna 415 bildpunkter tomt utrymme på höger sida av logotypen. Detta krävs för att lämna utrymme för textelement som ska bäddas programmässigt: ditt publisher visningsnamn plan titel, erbjuder lång sammanfattning.
- Hero-logotypen bakgrunden kanske inte svart, vit eller transparent. Kontrollera att din bakgrundsfärgen som används inte för ljusa eftersom inbäddad text visas i vitt.
- När du publicerar ditt erbjudande med en hero-ikon, inte du ta bort den (även om du kan uppdatera den med en annan version om du vill).

I den **leda Management** avsnittet CRM-systemet där dina leads sparas kan välja om du vill. 

Slutligen, ange din **princip Sekretesswebbadress** och **användningsvillkoren** i den **juridiska** avsnittet. Du kan också ange här om du ska använda eller inte den [standardavtal](https://docs.microsoft.com/azure/marketplace/standard-contract) för det här erbjudandet.

Glöm inte att spara dina ändringar innan du går in på den **Support** avsnittet.

## <a name="add-support-info"></a>Lägg till supportinformation

I den **stöder** avsnittet, ange namn, e-post och telefonnummer för en tekniska kontakt- och en kund Supportkontakt. Du måste också att ge stöd för URL: er. Microsoft kan använda den här informationen när vi behöver kontakta dig om företag och supportärenden.

När du har lagt till den här informationen, Välj **spara.**

## <a name="publish-your-offer"></a>Publicera ditt erbjudande

När du är nöjd med all den information som du har angett är nästa steg att publicera erbjudandet i Azure Marketplace. Välj den **publicera** för att starta processen så att ditt erbjudande live. Mer information om den här processen finns i [publicera Azure Marketplace och AppSource erbjudanden](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [mellan klientorganisationer hanteringsupplevelser](../concepts/cross-tenant-management-experience.md).
- [Visa och hantera kunder](view-manage-customers.md) genom att gå till **mina kunder** i Azure-portalen.
