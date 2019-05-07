---
title: Customer Lockbox för Microsoft Azure
description: Teknisk översikt över Customer Lockbox för Microsoft Azure, vilket ger kontroll över cloud-providerns åtkomst när Microsoft behöva åtkomst till kunddata.
author: cabailey
ms.service: security
ms.topic: article
ms.author: cabailey
manager: barbkess
ms.date: 05/07/2019
ms.openlocfilehash: 468e392cd2c45d79cbb24f8d737a6e83fbcd2725
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079277"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Customer Lockbox för Microsoft Azure

> [!NOTE]
> Om du vill använda denna funktion kan din organisation måste ha en [Azure-supportplan](https://azure.microsoft.com/support/plans/) med en miniminivå **Developer**.

Customer Lockbox för Microsoft Azure tillhandahåller ett gränssnitt för kunder att granska och godkänna eller avvisa dataåtkomstbegäranden som kunden. Den används i fall där en Microsoft-tekniker behöver åtkomst till kunddata under en supportförfrågan.

Den här artikeln beskriver hur Customer Lockbox begäranden påbörjades, spåras och lagras för senare granskning och granskningar.

Customer Lockbox är nu allmänt tillgänglig och är aktiverad för fjärrskrivbordsåtkomst till virtuella datorer.

## <a name="workflow"></a>Arbetsflöde

Följande steg beskriver ett vanligt arbetsflöde för en kund Lockbox-begäran.

1. Någon i en organisation har ett problem med sina Azure-arbetsbelastning.

2. När den här personen felsöker problemet, men det går inte att åtgärda det, de öppna ett supportärende från den [Azure-portalen](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Biljetten har tilldelats en supporttekniker för Azure-kund.

3. En Azure-supporttekniker granskar tjänstbegäran och anger nästa steg för att lösa problemet.

4. Om supportteknikern det går inte att felsöka problemet genom att använda standardverktyg och telemetri är nästa steg att begära förhöjd behörighet med hjälp av en tjänst för just JIT-åtkomst. Den här förfrågan kan vara från den ursprungliga supportteknikern. Eller så det kan vara från en annan tekniker eftersom eskaleras problemet till Azure DevOps-teamet.

5. När du har åtkomsten-begäran har skickats Azure-tekniker Just-In-Time tjänsten utvärderar begäran med hänsyn till faktorer som:
    - Omfånget för resursen
    - Om den som begär är en isolerad identitet eller använda Multi-Factor authentication
    - Behörighetsnivåer
    
    Baserat på JIT-regeln kan innefatta den här begäran också ett godkännande från den interna Microsoft godkännare. Godkännaren kan exempelvis vara Customer support lead eller DevOps Manager.

6. När begäran kräver direkt åtkomst till kunddata, initieras en Customer Lockbox-begäran. Till exempel fjärrskrivbordsåtkomst till en kunds virtuella datorn.
    
    Begäran är nu i en **kunden meddelas** tillstånd, väntar på kundens godkännande innan åtkomst beviljas.

7. På kundens organisation, den användare som har den [ägarrollen](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) för Azure-prenumeration får ett e-postmeddelande från Microsoft, för att meddela dem om väntande förfrågan. Den här personen finns för förfrågningar om Customer Lockbox avsedda godkännaren.
    
    Exempel e-postadress:
    
    ![Azure Customer Lockbox - e-postmeddelande](./media/azure-customer-lockbox/customer-lockbox-email-notification.png)

8. E-postmeddelandet innehåller en länk till den **Customer Lockbox** bladet i Azure-portalen. Med den här länken loggar utsedda användare in på Azure-portalen att visa alla väntande begäranden som organisationen har för Customer Lockbox:
    
    ![Azure Customer Lockbox - landningssida](./media/azure-customer-lockbox/customer-lockbox-landing-page.png)
    
   Begäran är kvar i kön för kunden i fyra dagar. Förfrågan upphör automatiskt efter den tidpunkten och ingen åtkomst beviljas till Microsoft-tekniker.

9. Om du vill hämta information om den väntande begäran avsedda godkännaren kan välja lockbox begäran från **väntande begäranden**:
    
    ![Azure Customer Lockbox - Visa väntande begäran](./media/azure-customer-lockbox/customer-lockbox-pending-requests.png)

10. Avsedda godkännaren kan också välja den **ID för TJÄNSTBEGÄRAN** att visa biljett supportförfrågan som har skapats med den ursprungliga användaren. Den här informationen ger kontext för varför Microsoft Support engagerade och historiken för det rapporterade problemet. Exempel:
    
    ![Azure Customer Lockbox - visa biljett supportförfrågan](./media/azure-customer-lockbox/customer-lockbox-support-ticket.png)

11. När du har granskat begäran avsedda godkännaren väljer **Godkänn** eller **neka**:
    
    ![Azure Customer Lockbox - Välj Godkänn eller neka](./media/azure-customer-lockbox/customer-lockbox-approval.png)
    
    Till följd av markeringen:
    - **Godkänn**:  Komma åt Microsoft-tekniker. Åtkomst beviljas för en standardperiod på åtta timmar.
    - **Neka**: Utökad åtkomst-begäran från Microsoft-tekniker avvisas och ytterligare åtgärd ingen.

I granskningssyfte, de åtgärder som vidtagits i det här arbetsflödet är inloggad [Customer Lockbox begära loggar](#auditing-logs).

## <a name="auditing-logs"></a>Granskningsloggar

Customer Lockbox loggar lagras i aktivitetsloggarna. I Azure-portalen väljer du **aktivitetsloggar** att visa granskningsinformation som är relaterade till Customer Lockbox begäranden. Du kan filtrera efter specifika åtgärder, till exempel:
- **Neka begäran Lockbox**
- **Skapa begäran om Lockbox**
- **Godkänn begäran om Lockbox**
- **Lockbox begäran slutar att gälla**

Som exempel:

![Azure Customer Lockbox - aktivitetsloggar](./media/azure-customer-lockbox/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios"></a>Tjänster som stöds och scenarier

Följande tjänster och scenarier är för närvarande allmänt tillgängliga för Customer Lockbox.

### <a name="remote-desktop-access-to-virtual-machines"></a>Fjärrskrivbordsåtkomst till virtuella datorer

Customer Lockbox är aktiverat för fjärrskrivbordsåtkomst begäranden till virtuella datorer. Följande arbetsbelastningar stöds:
- Plattform som en tjänst (PaaS) - version 1
- Infrastruktur som en tjänst (IaaS) -, Windows och Linux (endast Azure Resource Manager)
- Virtual machine scale Sets – Windows- och Linux

> [!NOTE]
> IaaS Classic instanser stöds inte av Customer Lockbox. Om du har arbetsbelastningar som körs på IaaS Classic-instanser, rekommenderar vi att du migrerar dem från klassisk till Resource Manager-distributionsmodeller. Anvisningar finns i [plattformsunderstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Detaljerade granskningsloggar

Du kan använda Windows-händelseloggar för scenarier som innefattar fjärrskrivbordsåtkomst, för att granska de åtgärder som vidtas av Microsoft-tekniker. Överväg att använda Azure Security Center att samla in händelseloggarna och kopiera data till din arbetsyta för analys. Mer information finns i [insamling av Data i Azure Security Center](../security-center/security-center-enable-data-collection.md).

## <a name="exclusions"></a>Undantag

Customer Lockbox begäranden utlöses inte i följande tekniska supportscenarier:

- En Microsoft-tekniker behöver för att utföra en aktivitet som faller utanför standardprocedurer. Till exempel att återställa eller återställa tjänster i oväntade eller oförutsägbara scenarier.

- En Microsoft-tekniker har åtkomst till Azure-plattformen som en del av felsökning och har oavsiktligt åtkomst till kunddata. Till exempel utför Azure Network-teamet felsökning som resulterar i ett infångat paket på en nätverksenhet. Om kunden krypterade data under överföring kan kan inte teknikern läsa data.

## <a name="next-steps"></a>Nästa steg

Customer Lockbox är automatiskt tillgängligt för alla kunder som har en [Azure-supportplan](https://azure.microsoft.com/support/plans/) med en miniminivå **Developer**.

När du har ett supportavtal för berättigade, krävs ingen åtgärd av dig att aktivera Customer Lockbox. Återkopplingen Lockbox initieras av en Microsoft-tekniker om den här åtgärden krävs hängt ett supportärende som har arkiverats från någon i din organisation.
