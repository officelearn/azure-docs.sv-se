---
title: Ordna dina resurser med Azure Hanteringsgrupper - Azure | Microsoft Docs
description: "Lär dig mer om av hanteringsgrupper och hur de används."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: 18541c68b02ae1b59ae4a6a85122dff614c9978c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Ordna dina resurser med Azure-Hanteringsgrupper 

Om du har flera prenumerationer kan du ordna dem i behållare som kallas ”hanteringsgrupper” för att hantera åtkomst, princip, kostnader och kompatibilitet för alla prenumerationer. Exempelvis kan du tillämpa principer till en hanteringsgrupp att begränsa vilka typer av resurser kan skapas.

> [!Note]
> Den här funktionen är för tillfället i privat förhandsvisning. [Registrera dig här](https://aka.ms/MGPreviewSignup) ha din registrering ansluta förhandsgranskningen.   
 


Hanteringsgrupper kan ordnas i en hierarki. Strukturen är en exempel-representation av en hierarki för hantering av grupp som kan finnas:


![Hierarkiträd](media/billing-enterprise-mgmt-groups/tree.png)



## <a name="how-management-groups-are-related-to-your-azure-enterprise-enrollment"></a>Hur hanteringsgrupper är relaterade till din Azure Enterprise-registrering

Införandet av hanteringsgrupper är ett steg i större transporten av övergång [Enterprise portal](https://ea.azure.com) funktioner till den [Azure-portalen](https://portal.azure.com).

Strukturen för hantering av skapas som det definierats i Enterprise portal. Hela hierarkin som består av registrering, avdelningar och konton mappas till motsvarande hanteringsgrupper. 

Här är hur aktuella EA strukturen mappar till hantering av hierarkin. 

![Hierarkiträd](media/billing-enterprise-mgmt-groups/tree2.png)

Tabellen nedan visar hur användare från Enterprise portal mappas till gruppen hanteringshierarkin.

|    EA roll                                       |    Rollen på noden för hantering av mappad grupp    |    Behörigheter för gruppen hanteringsnod                                                          |
|--------------------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------|
|    EA-administratör                              |    Resursen princip deltagare                   |    Kan visa kostnader, hantera resurs principen och visa hierarki på och under noden registrering    |
|    EA administratör i skrivskyddat läge            |    Fakturering läsare                                |    Kan läsa kostnader och visa hierarki på och under noden registrering                              |
|    Avdelning administratör                      |    Fakturering läsare                                |    Kan läsa kostnader och visa hierarki och under noden avdelning                                 |
|    Avdelning administratör i skrivskyddat läge    |    Fakturering läsare                                |    Kan läsa kostnader och visa hierarki och under noden avdelning                                 |
|    Kontoägaren                                 |    Resursen princip deltagare                   |    Kan visa kostnader, hantera resurs principen och visa hierarki på och under noden konto       |




## <a name="view-management-groups-in-the-azure-portal"></a>Visa hantering av grupper i Azure-portalen

Om du vill visa en registrering, avdelning eller ett konto i förhandsgranskningen, logga in på Azure-portalen med länken i välkomstmeddelandet.   

![Hierarki](media/billing-enterprise-mgmt-groups/hierarchy.png)

### <a name="viewing-costs"></a>Visa kostnader 
Detalj skärmar hanteringsgrupper visas de aktuella månad-till-date-kostnaderna. Kostnaderna baseras på användning och hänsyn inte till förskottsinbetalade belopp, överskott, inkluderade kvantiteter, justeringar och skatter. Management-grupp som motsvarar din registrering, visar avsnittet kostnader för åtagande kvar.  

![registrering-detaljer](media/billing-enterprise-mgmt-groups/enrollment.png)

 ”Kostnader debiteras separat” är månatliga anhopning av separata ändringar som marketplace, överskott och övriga kostnader som inte går mot engagemang för din registrering.  Läs mer om kostnadsuppdelning av [Enterprise portal](https://ea.azure.com). 

### <a name="enabling-access-to-costs"></a>Aktivera åtkomst till kostnader
Om du inte ser kostnader, finns våra [Felsök enterprise kostnad vyer](https://aka.ms/enableazurecosts) dokument om du behöver hjälp.  

### <a name="delays-between-the-enterprise-portal-and-azure-portal"></a>Fördröjningar mellan Enterprise portal och Azure-portalen 
* I förhandsversionen kan belopp som visas i Azure portal fördröjas jämfört med om du värdena i Enterprise portal. Det här problemet är tillfälligt och utförs på.
* Uppdaterade inställningarna i Enterprise portal har en fördröjning på flera minuter innan uppdateringarna visas i Azure-portalen. 

## <a name="management-groups-have-a-relationship-with-your-directory"></a>Hanteringsgrupper som har en relation med din katalog   
Som prenumerationer ha hanteringsgrupper också en förtroenderelation med Azure AD. En grupp hanteringshierarkin förtroende för en katalog för att autentisera användare. Alla administratörer som är kopplade till en hierarki för management-grupp måste tillhöra samma katalog. 

Eftersom registrering av hierarkin är mappad till hanteringsgrupper upprätta en förtroenderelation med en katalog. Om möjligt väljs en befintlig katalog som är associerade med den registrering användarkonton. I vissa fall skapas en ny katalog och alla befintliga användare har bjudits in till den katalogen. Som har associerats med den registrering prenumerationer påverkas inte. Därför kan hierarkin skapas i en katalog som skiljer sig från prenumerationerna. [Lär dig mer](billing-enterprise-mgmt-grp-find.md) om hur den här processen påverkar upplevelse av att navigera mellan en hierarki och dess prenumerationer.

## <a name="administering-your-management-groups"></a>Administrera dina hanteringsgrupper
Av hanteringsgrupper i Azure portal finns i förhandsgranskningen och är skrivskyddade i den här första utgåvan. Om du vill se alla uppdateringar går du till Enterprise portal. Dina uppdateringar visas automatiskt i Azure-portalen. Finns i dokumentationen i enterprise portal för att få hjälp om hur du gör ändringar och tillägg.   

## <a name="policy-management"></a>Hantering av Grupprincip
Med Resource Manager kan du hantera resurser genom att skapa anpassade principer. Med av hanteringsgrupper principer kan tilldelas på alla nivåer i hierarkin och resurserna som ärver dessa principer.  [Läs mer](https://go.microsoft.com/fwlink/?linkid=858942)

> [!Note]
> Principen aktiveras inte i kataloger. 


