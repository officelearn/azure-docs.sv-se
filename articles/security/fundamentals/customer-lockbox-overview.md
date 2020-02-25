---
title: Customer Lockbox för Microsoft Azure
description: Teknisk översikt över Customer Lockbox för Microsoft Azure, som ger kontroll över moln leverantörs åtkomst när Microsoft kan behöva åtkomst till kund information.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: abc16ae7f7ab8bf15173248a6e7668e689e127de
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561977"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Customer Lockbox för Microsoft Azure

> [!NOTE]
> Din organisation måste ha ett support avtal för [Azure](https://azure.microsoft.com/support/plans/) med en minimal **utvecklings**nivå för att använda den här funktionen.

Customer Lockbox för Microsoft Azure ger ett gränssnitt för kunder som kan granska och godkänna eller avvisa förfrågningar om kund data åtkomst. Den används i fall där en Microsoft-tekniker behöver åtkomst till kund information under en support förfrågan.

Den här artikeln beskriver hur Customer Lockbox begär Anden initieras, spåras och lagras för senare granskningar och granskningar.

Customer Lockbox är nu allmänt tillgänglig och för närvarande aktiverat för fjärr skrivbords åtkomst till virtuella datorer.

## <a name="workflow"></a>Arbetsflöde

Följande steg beskriver ett typiskt arbets flöde för en Customer Lockbox begäran.

1. Någon i en organisation har ett problem med sina Azure-arbetsbelastningar.

2. När den här personen har felsöker problemet, men inte kan åtgärda det, öppnar de ett support ärende från [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Biljetten tilldelas en support tekniker för Azure-kunder.

3. En support tekniker för Azure granskar tjänstbegäran och bestämmer nästa steg för att lösa problemet.

4. Om support teknikern inte kan felsöka problemet med hjälp av standard verktyg och telemetri, är nästa steg att begära utökade behörigheter genom att använda en just-in-Time (JIT)-åtkomst tjänst. Den här begäran kan hämtas från den ursprungliga support teknikern. Eller så kan det vara från en annan tekniker eftersom problemet eskaleras till Azure DevOps-teamet.

5. När åtkomstbegäran har skickats av Azure-teknikern utvärderar just-in-Time-tjänsten begäran med hänsyn till faktorer som:
    - Resursens omfattning
    - Om beställaren är en isolerad identitet eller med Multi-Factor Authentication
    - Behörighets nivåer

    Den här begäran kan även innehålla ett godkännande från interna Microsoft-god kännare, baserat på en JIT-regel. God kännaren kan till exempel vara kund support eller DevOps Manager.

6. När begäran kräver direkt åtkomst till kund information initieras en Customer Lockbox begäran. Till exempel fjärr skrivbords åtkomst till en kunds virtuella dator.

    Begäran **besvaras nu i ett Kundmeddelat** tillstånd och väntar på kundens godkännande innan åtkomst beviljas.

7. Vid kund organisationen får användaren som har [ägar rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) för Azure-prenumerationen ett e-postmeddelande från Microsoft för att meddela dem om den väntande åtkomst förfrågan. Den här personen är utsedd god kännare för Customer Lockbox begär Anden.

    Exempel på e-post:

    ![Azure Customer Lockbox – e-postavisering](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. E-postmeddelandet innehåller en länk till bladet **Customer lockbox** i Azure Portal. Med den här länken loggar den utsedda god kännaren in på Azure Portal för att visa väntande begär Anden som deras organisation har för Customer Lockbox:

    ![Sidan Azure Customer Lockbox-landning](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   Begäran finns kvar i kund kön i fyra dagar. Efter den här tiden upphör åtkomst förfrågan automatiskt att gälla och ingen åtkomst beviljas till Microsoft-tekniker.

9. För att få information om den väntande begäran kan den utsedda god kännaren välja begäran om säker begäran från **väntande begär Anden**:

    ![Azure Customer Lockbox – Visa väntande begäran](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. Den angivna god kännaren kan också välja **ID för tjänstbegäran** för att se begäran om support ärende som skapades av den ursprungliga användaren. Den här informationen innehåller en kontext för varför Microsoft Support aktive ras och historiken för det rapporterade problemet. Exempel:

    ![Azure Customer Lockbox – Visa begäran om support ärende](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Efter att ha granskat begäran väljer den utsedda god kännaren **Godkänn** eller **neka**:

    ![Azure-Customer Lockbox – Välj Godkänn eller neka](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    Som ett resultat av valet:
    - **Godkänn**: åtkomst beviljas till Microsoft-teknikern. Åtkomst beviljas för en standard period på åtta timmar.
    - **Neka**: den utökade åtkomst förfrågan från Microsoft-teknikern avvisas och ingen ytterligare åtgärd vidtas.

I gransknings syfte loggas de åtgärder som vidtas i det här arbets flödet i [Customer lockbox begär Anden](#auditing-logs).

## <a name="auditing-logs"></a>Gransknings loggar

Customer Lockbox loggar lagras i aktivitets loggar. I Azure Portal väljer du **aktivitets loggar** för att Visa gransknings information som är relaterad till Customer lockbox begär Anden. Du kan filtrera efter vissa åtgärder, till exempel:
- **Neka begäran om säker databas**
- **Begäran om att skapa säker databas**
- **Godkänn begäran om att säkra databasen**
- **Förfallo datum för säker begäran**

Som exempel:

![Azure Customer Lockbox-aktivitets loggar](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Tjänster och scenarier som stöds i allmän tillgänglighet

Följande tjänster och scenarier är för närvarande allmänt tillgängliga för Customer Lockbox.

### <a name="remote-desktop-access-to-virtual-machines"></a>Fjärr skrivbords åtkomst till virtuella datorer

Customer Lockbox är för närvarande aktiverat för förfrågningar om fjärr skrivbords åtkomst till virtuella datorer. Följande arbets belastningar stöds:
- PaaS (Platform as a Service) – Azure Cloud Services (webb roll och arbets roll)
- IaaS (Infrastructure as a Service) – Windows och Linux (endast Azure Resource Manager)
- Skalnings uppsättning för virtuella datorer – Windows och Linux

> [!NOTE]
> IaaS klassiska instanser stöds inte av Customer Lockbox. Om du har arbets belastningar som körs på IaaS klassiska instanser rekommenderar vi att du migrerar dem från klassiska till Resource Manager-distributions modeller. Instruktioner finns i [plattforms stöd för migrering av IaaS-resurser från klassisk till Azure Resource Manager](../../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Detaljerade gransknings loggar

För scenarier som involverar åtkomst till fjärr skrivbord kan du använda Windows-händelseloggen för att granska de åtgärder som vidtagits av Microsoft-teknikern. Överväg att använda Azure Security Center för att samla in dina händelse loggar och kopiera data till din arbets yta för analys. Mer information finns [i data insamling i Azure Security Center](../../security-center/security-center-enable-data-collection.md).

## <a name="supported-services-and-scenarios-in-preview"></a>Tjänster och scenarier som stöds i för hands version

Följande tjänster är nu i för hands version för Customer Lockbox:

- Azure Storage

- Azure SQL-databas

- Azure-datautforskaren

- Virtuella datorer (nu omfattar även åtkomst till minnes dum par och hanterade diskar)

- Azure-prenumerations överföringar

Om du vill aktivera Customer Lockbox för dessa förhands gransknings erbjudanden för din organisation kan du registrera dig för [Customer lockbox för offentlig för hands version av Azure](https://aka.ms/customerlockbox/insiderprogram)


## <a name="exclusions"></a>Undantag

Customer Lockbox förfrågningar utlöses inte i följande tekniska support scenarier:

- En Microsoft-tekniker måste utföra en aktivitet som ligger utanför standard drifts procedurer. Till exempel för att återställa eller återställa tjänster i oväntade eller oförutsägbara scenarier.

- En Microsoft-tekniker får åtkomst till Azure-plattformen som en del av fel sökning och oavsiktligt har åtkomst till kunddata. Till exempel kan Azures nätverks team utföra fel sökning som resulterar i en paket avbildning på en nätverks enhet. Men om kunden har krypterat data under överföringen, kan inte teknikern läsa data.

## <a name="next-steps"></a>Nästa steg

Customer Lockbox är automatiskt tillgängligt för alla kunder som har en [Support plan för Azure](https://azure.microsoft.com/support/plans/) med en minimal **utvecklings**nivå.

När du har en berättigad Support plan krävs ingen åtgärd för att aktivera Customer Lockbox. Customer Lockbox förfrågningar initieras av en Microsoft-tekniker om den här åtgärden krävs för att försätta ett support ärende som har arkiverats från någon i din organisation.
