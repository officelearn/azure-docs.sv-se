---
title: Dynamics 365 för kundengagemang erbjuder krav – Azure Marketplace | Microsoft Docs
description: Krav för att publicera ett Azure-program erbjuder på Azure Marketplace.
services: Dynamics 365 for Customer Engagement offer, Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: pbutlerm
ms.openlocfilehash: 4b4859c41e7a3903de68b62e8587f1c85805a782
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54083334"
---
# <a name="dynamics-365-for-customer-engagement-prerequisites"></a>Dynamics 365 för kundengagemang förutsättningar

Den här artikeln beskriver tekniska och affärsmässiga förutsättningarna för att publicera en Dynamics 365 för kundengagemang programmet erbjudandet på AppSource-marknadsplatsen.


## <a name="technical-requirements"></a>Tekniska krav

Din Dynamics 365 för kundengagemang program måste uppfylla de [Microsoft AppSource app granskningsriktlinjer](https://smp-cdn-prod.azureedge.net/documents/AppsourceGuidelines/Microsoft%20AppSource%20app%20review%20guidelines_v5.pdf), vilket inkluderar följande krav:


|              Krav             |        Beskrivning           |
|            ---------------           |      ---------------         |
| Azure Active Directory-integrering   | Din app måste tillåta Azure Active Directory federerad enkel inloggning (AAD-federerad enkel inloggning) med medgivande aktiverat. Mer information finns i [så här hämtar du AppSource-certifierat för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-get-appsource-certified). |
| Integrering med Microsoft Cloud-tjänsterna (valfritt) | Om den här funktionen krävs, bör din app integrera med andra Microsoft Cloud-tjänster som Microsoft Power BI, Microsoft Flow eller Microsoft Azure-tjänster som machine learning eller cognitive services. |
| Fokuserar Line-of-business            |  Din app måste fokusera på en väldefinierad affärsprocess eller utfärda, främst rikta företagskunder och användarna kan logga in med sina autentiseringsuppgifter (användarnamn och lösenord).  |
| Kostnadsfria utvärderingsperioden och utvärderingsmiljö |  En kund måste kunna använda appen utan kostnad under en begränsad tid: antingen en ”Hämta nu” för kostnadsfria appar, en ”kostnadsfri utvärdering” för en angiven tidsperiod, en ”Testkör” demonstrator eller en ”kontakta mig” be alternativet.  |
| Nej eller med lite konfiguration                 | Din app måste vara enkelt och snabbt att göra inställningar (inga utveckling eller anpassning krävs).  |
| Kundsupport                     | Stöd för din app måste innehålla en supportlänk där kunder kan få hjälp.  |
| Tillgänglighet/drifttid                  | Din app måste ha en drifttid på minst 99,9%. |
|  |  |


## <a name="business-requirements"></a>Affärskrav

Affärskraven omfattar följande procedur, avtalsenliga och juridiska skyldigheter:

* Du måste vara registrerad på den [Microsoft Partner Network (MPN)](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx) eller vara en registrerad molnet Marketplace-utgivare. Om du inte är registrerad, följer du stegen i [blir molnet Marketplace utgivare](../../become-publisher.md).  (Använd i stället för det tredje steget i [AppSource partner nomineringsformuläret](https://appsource.microsoft.com/partners/signup)). 

    >[!NOTE]
    >Du bör använda samma konto för registrering av Microsoft Developer Center för att logga in på partnerportalen i molnet. Du bör ha endast en Microsoft-konto för alla dina Azure Marketplace-erbjudanden. Det här kontot får inte vara specifika för enskilda tjänster eller erbjudanden.

* Eftersom en commerce-aktiverade publiceringsalternativ inte omfattas av AppSource, måste du använda den aktuella sorteringen och fakturering infrastrukturen utan ytterligare investeringar eller ändringar.
* Du är ansvarig för att göra teknisk support tillgänglig för kunder på ett kommersiellt rimligt sätt. Detta stöd kan vara kostnadsfria, betald, eller via community-metoder.
* Du är ansvarig för att licensiera ditt program och eventuella beroenden för programvara från tredje part.
* Du bör har skapat den associerade marknadsföringsmaterial, till exempel en officiella namn, beskrivning (i HTML-format), logotypbilder i PNG-format (40 x 40, 90 x 90, 115 x 115 och 255 x 115 bildpunkter) och användningsvillkor och en sekretesspolicy.  


## <a name="next-steps"></a>Nästa steg

När du har uppfyllt dessa krav, kan du [skapa erbjudande om en Dynamics 365 för kundengagemang](./cpp-create-offer.md) 
