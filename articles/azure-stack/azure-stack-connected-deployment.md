---
title: Integrerade Azure anslutna distributionsbeslut för Azure Stack-system | Microsoft Docs
description: Kontrollera distributionen planeringsbeslut för flera noder Azure Stack Azure-ansluten distributioner.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: d64b834f1c6794976461c93d4ad1d05f8647e986
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414597"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Integrerade Azure-ansluten distribution planeringsbeslut för Azure Stack-system
När du har bestämt dig [hur du ska integrera Azure Stack i hybridmolnmiljön](azure-stack-connection-models.md), du kan sedan slutföra din Azure Stack-distributionsbeslut.

Distribuera Azure Stack är ansluten till Azure innebär att du kan ha Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) för din identitet butik. Du kan också välja från antingen faktureringsmodell: betalning som du-användning eller kapacitetsbaserad. En ansluten distribution är standardalternativet eftersom kan kunderna få ut maximalt av Azure Stack, särskilt för hybridmolnscenarierna som omfattar både Azure och Azure Stack. 

## <a name="choose-an-identity-store"></a>Välj en identity-butik
Du kan välja mellan Azure AD eller AD FS för din identitetsarkiv med en ansluten distribution. En frånkopplad distribution kan som saknar Internetanslutning bara använda AD FS.

Valfri identitet store påverkar inte virtuella datorer (VM). Virtuella klientdatorer kan välja vilka identitetsarkiv som de vill ansluta till beroende på hur de ska konfigureras: Azure AD, Windows Server Active Directory-domänanslutna, arbetsgrupp, osv. Det här är inte relaterat till Azure Stack identitet providern beslut. 

Till exempel om du distribuerar virtuella ovanpå Azure Stack för IaaS-klientdatorer och vill att de ska ansluta till en företagets Active Directory-domän och använda konton därifrån, kan du fortfarande göra detta. Du behöver inte använda Azure AD-identitetsarkiv som du väljer här för dessa konton.

### <a name="azure-ad-identity-store"></a>Azure AD-identitetsarkiv
När du använder Azure AD för din identitetsarkiv kräver två Azure AD-konton: ett globalt administratörskonto och ett faktureringskonto. Dessa konton kan vara samma konton eller olika konton. Med hjälp av samma användarkonto kan vara enklare och användbart om du har ett begränsat antal Azure-konton, kan dina affärsbehov föreslår att du använder två konton:

1. **Globala administratörskonto** (krävs endast för anslutna distributioner). Det här är ett Azure-konto som används för att skapa program och tjänstens huvudnamn för Azure Stack infrastrukturtjänster i Azure Active Directory. Det här kontot måste ha administratörsbehörighet för katalogen till den katalog som Azure Stack-system ska distribueras under. Blir det ”cloud-operatör” Global administratör för Azure AD-klient och kommer att användas: 
    - För att etablera och delegera program och tjänstens huvudnamn för alla Azure Stack-tjänster som måste interagera med Azure Active Directory och Graph API. 
    - Som tjänstadministratör-kontot. Det här är ägare av prenumerationen för standard-provider (som du kan ändra senare). Du kan logga in på administrationsportalen för Azure Stack med det här kontot och använda den för att skapa erbjudanden och planer, ange kvoter och utföra andra administrativa funktioner i Azure Stack.
2. **Faktureringskonto** (krävs för både anslutas och kopplas distributioner). Den här Azure-konto används för att upprätta faktureringsrelation mellan integrerade Azure Stack-system och Azure commerce-serverdelen. Det här är det konto som kommer att debiteras för Azure Stack-avgifter. Det här kontot används också för att erbjuda objekt i marketplace och andra hybridscenarier. 

### <a name="ad-fs-identity-store"></a>AD FS-identitetsarkiv
Välj det här alternativet om du vill använda din egen identitet lager, till exempel din företagets Active Directory för din tjänst administratörskonton.  

## <a name="choose-a-billing-model"></a>Välj faktureringsmodellen
Du kan välja antingen **betalning som du-användning** eller **kapacitet** faktureringsmodell. Betalning som du-användning fakturering modelldistributioner måste kunna rapportera användning via en anslutning till Azure minst en gång var 30: e dag. Därför är faktureringsmodellen betalning som du-användning endast tillgängligt för anslutna distributioner.  

### <a name="pay-as-you-use"></a>Betalning som du-användning
Med faktureringsmodell betalning som du-användning debiteras användningen till en Azure-prenumeration. Du betalar bara när du använder Azure Stack-tjänster. Om det här är den modell som du väljer du behöver en Azure-prenumeration och konto-ID som är associerade med den aktuella prenumerationen (till exempel serviceadmin@contoso.onmicrosoft.com). EA, CSP och CSL prenumerationer stöds. Användningsrapport konfigureras under [Azure Stack-registrering](azure-stack-registration.md).

> [!NOTE]
> I de flesta fall företagskunder ska använda EA-prenumerationer och tjänstleverantörer använder CSP eller CSL prenumerationer.

Om du ska använda en CSP-prenumeration kan du granska tabellen nedan för att identifiera vilka CSP-prenumeration för att använda, eftersom det korrekta sättet är beroende av det exakta CSP-scenariot:

|Scenario|Alternativ för domänen och prenumeration|
|-----|-----|
|Du är en **direkt CSP-Partner** eller en **indirekta CSP-Provider**, och du kommer att fungera Azure Stack|Använd en CSL (Common Service Layer)-prenumeration.<br>     eller<br>Skapa en Azure AD-klient med ett beskrivande namn i Partnercenter. Till exempel &lt;organisationen > CSPAdmin med en Azure CSP-prenumeration som är kopplade till den.|
|Du är en **indirekta CSP-återförsäljare**, och du kommer att fungera Azure Stack|Be din indirekta CSP-Provider för att skapa en Azure AD-klient för din organisation med en Azure CSP-prenumeration som är associerade med den med hjälp av Partner Center.|

### <a name="capacity-based-billing"></a>Kapacitet baserat fakturering
Om du vill använda faktureringsmodellen kapacitet måste du köpa en Azure Stack kapacitet planera SKU som baseras på kapaciteten för datorn. Du behöver veta antalet fysiska kärnor i din Azure Stack köpa rätt antal. 

Kapacitet fakturering kräver ett Enterprise Agreement (EA) Azure-prenumeration för registrering. Anledningen är att registreringen ställer in tillgängligheten för objekt i Marketplace, vilket kräver en Azure-prenumeration. Prenumerationen används inte för användning av Azure Stack.

## <a name="learn-more"></a>Läs mer
- Information om användningsfall, inköp, partner och OEM maskinvaruleverantörer finns i den [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) produktsidan.
- Mer information om plan och geo-tillgänglighet för Azure Stack-integrerade system finns i faktabladet: [Azure Stack: en utökning av Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Mer information om Microsoft Azure Stack förpackning och priser [ladda ned .pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Nästa steg
[Datacenter nätverksintegrering](azure-stack-network.md)