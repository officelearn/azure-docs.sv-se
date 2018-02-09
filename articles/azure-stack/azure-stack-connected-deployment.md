---
title: "Azure anslutna distributionsbeslut för Azure-Stack integrerat system | Microsoft Docs"
description: "Kontrollera distributionen planeringsbeslut för flera noder Azure Stack Azure-anslutna distributioner."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: def9d5381144026b5ad0e8a076edd3c0692a08f4
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Azure-anslutna distribution planeringsbeslut för Azure-stacken integrerat system
När du har valt [hur du ska integrera Azure Stack i molnmiljön hybrid](azure-stack-connection-models.md), du kan sedan slutföra din Azure-stacken distributionsbeslut.

Distribuera Azure-stacken som är ansluten till Azure innebär att du kan ha Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) för identitets-butiken. Du kan också välja från antingen fakturering modell: lön-som-du-användning eller kapacitet-baserade. En ansluten distribution är standardalternativet eftersom ger kunder möjlighet att hämta de mest värdet utanför Azure-stacken, särskilt för hybrid cloud scenarier som omfattar både Azure och Azure-stacken. 

## <a name="choose-an-identity-store"></a>Välj en identity-butik
Du kan välja mellan Azure AD eller AD FS för din identitet butik med en ansluten distribution. En frånkopplad distribution kan med utan internet-anslutning bara använda AD FS.

Önskat identitet store påverkar inte virtuella datorer (VM). Virtuella datorer som klienten kan välja vilka Identitetslagret som de vill för att ansluta till beroende på hur de konfigureras: Azure AD och Windows Server Active Directory-domänanslutna, arbetsgrupp, osv. Detta är inte relaterat till Azure-stacken identitet providern beslut. 

Till exempel om du distribuerar IaaS klient virtuella datorer på Azure-stacken och vill att de ska ansluta till en företagets Active Directory-domän och använda konton därifrån, kan du fortfarande göra detta. Du behöver inte använda Azure AD identity store som du väljer här för dessa konton.

### <a name="azure-ad-identity-store"></a>Azure AD identity store
När du använder Azure AD för din identitet store kräver två konton i Azure AD: ett globalt administratörskonto och ett faktureringskonto. Dessa konton kan vara samma konton eller olika konton. När med samma användarkonto kan vara enklare och användbart om du har ett begränsat antal Azure-konton, kan behoven för din verksamhet föreslår att du använder två konton:

1. **Globala administratörskonto** (krävs endast för anslutna distributioner). Det här är ett Azure-konto som används för att skapa program och tjänstens huvudnamn för Azure-stacken infrastrukturtjänster i Azure Active Directory. Det här kontot måste ha administratörsbehörighet för katalog till katalogen som Azure Stack systemet ska distribueras under. Det blir operatorn ”moln” Global administratör för Azure AD-klient och kommer att användas: 
    - Att etablera och delegera program och tjänstens huvudnamn för alla Azure Stack-tjänster som behöver arbeta med Azure Active Directory och Graph API. 
    - Som tjänstadministratör-konto. Detta är ägare till prenumerationen för standard-providern (som du kan ändra senare). Du kan logga in på administrationsportalen för Azure-stacken med det här kontot och använda den för att skapa erbjudanden och planer, ange kvoter och utföra andra administrativa funktioner i Azure-stacken.
2. **Faktureringskonto** (krävs för både ansluten och frånkopplad distributioner). Den här Azure-konto används för att upprätta faktureringsrelation mellan din Azure-stacken integrerat system och Azure commerce-serverdelen. Detta är det konto som kommer att debiteras för Azure-stacken avgifter. Det här kontot används också för marketplace syndikering och andra hybridmoln. 

### <a name="ad-fs-identity-store"></a>AD FS Identitetslagret
Välj det här alternativet om du vill använda din egen identitet store, till exempel din företagets Active Directory för din tjänst administratörskonton.  

## <a name="choose-a-billing-model"></a>Välj faktureringsadministratörer modell
Du kan välja antingen **lön-som-du-Använd** eller **kapacitet** faktureringsmodell som tillämpas. Betala per-som-du-Använd fakturering modellen distributioner måste kunna rapportera användning via en anslutning till Azure minst en gång var 30: e dag. Därför är lön-som-du-Använd fakturering modellen endast tillgängligt för anslutna distributioner.  

### <a name="pay-as-you-use"></a>Betala per-som-du-användning
Med fakturering modellen lön-som-du-Använd debiteras användning till en Azure-prenumeration. Du betalar bara när du använder Azure Stack-tjänster. Om det här är den modell som du väljer du behöver en Azure-prenumeration och konto-ID som är kopplade till den prenumerationen (till exempel serviceadmin@contoso.onmicrosoft.com). EA och CSP CSL prenumerationer stöds. Användningsrapport konfigureras under [Azure Stack registrering](azure-stack-registration.md).

> [!NOTE]
> I de flesta fall Enterprise-kunder använder EA prenumerationer och leverantörer använder CSP eller CSL prenumerationer.

Om du ska använda en CSP-prenumeration kan du granska tabellen nedan för att identifiera vilka CSP-prenumeration för att använda, eftersom det korrekta sättet beror på det exakta CSP-scenariot:

|Scenario|Alternativ för domänen och prenumeration|
|-----|-----|
|Du är en **direkt CSP Partner** eller en **indirekt kryptografiprovidern**, och du kommer att fungera i Azure-stacken|Använda en prenumeration på CSL (Common Service Layer).<br>     eller<br>Skapa en Azure AD-klient med ett beskrivande namn i Partnercenter. Till exempel &lt;organisationen > CSPAdmin med en CSP för Azure-prenumeration som är kopplade till den.|
|Du är en **indirekt CSP återförsäljare**, och du kommer att fungera i Azure-stacken|Be leverantören indirekt CSP för att skapa en Azure AD-klient för din organisation med en CSP för Azure-prenumeration som är kopplade till den med hjälp av Partner Center.|

### <a name="capacity-based-billing"></a>Kapacitet baserat fakturering
Om du vill använda kapacitet fakturering modellen måste du köpa en Azure-stacken kapacitet planera SKU baserat på kapaciteten för systemet. Du behöver veta antalet fysiska kärnor i Azure-stacken köpa rätt antal. 

Kapacitet fakturering kräver en Enterprise-avtal (EA) Azure-prenumeration för registrering. Anledningen är att registreringen ställer in syndikeringsfeed som kräver en Azure-prenumeration. Prenumerationen används inte för användning i Azure-stacken.

## <a name="learn-more"></a>Läs mer
- Information om användningsområden, köpa, partners och OEM maskinvaruleverantörer finns i [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) produktsidan.
- Information om plan och geo tillgänglighet för Azure-stacken integrerade system finns i faktabladet: [Azure stacken: ett tillägg för Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Läs mer om Microsoft Azure-stacken paketera och prissättning [ladda ned PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Nästa steg
[Datacenter nätverksintegration](azure-stack-network.md)