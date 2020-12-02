---
title: Customer Lockbox för Microsoft Azure
description: Teknisk översikt över Customer Lockbox för Microsoft Azure, som ger kontroll över moln leverantörs åtkomst när Microsoft kan behöva åtkomst till kund information.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 09/15/2020
ms.openlocfilehash: 01232aa101e2964354acfbeb6cea341a0da33ca6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489901"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Customer Lockbox för Microsoft Azure

> [!NOTE]
> Din organisation måste ha ett support avtal för [Azure](https://azure.microsoft.com/support/plans/) med en minimal **utvecklings** nivå för att använda den här funktionen.

Customer Lockbox för Microsoft Azure tillhandahåller ett gränssnitt med vilket kunderna kan granska och godkänna eller avvisa begäranden om kunddataåtkomst. Den används i fall där en Microsoft-tekniker behöver åtkomst till kundinformation under en supportförfrågan.

Den här artikeln beskriver hur Customer Lockbox begär Anden initieras, spåras och lagras för senare granskningar och granskningar.

Customer Lockbox är nu allmänt tillgängligt och för närvarande aktiverat för fjärrskrivbordsåtkomst till virtuella datorer.

## <a name="supported-services-and-scenarios-in-preview"></a>Tjänster och scenarier som stöds i för hands version

Följande tjänster är nu i förhandsversion för Customer Lockbox:

- API Management
- Azure App Service
- Cognitive Services
- Container Registry
- Azure Database for MySQL
- Azure Databricks
- Azure Data Box
- Azure-datautforskaren
- Azure Data Factory
- Azure Database for PostgreSQL
- Azure Functions
- HDInsight
- Azure Kubernetes Service
- Azure Monitor
- Azure Storage
- Azure SQL-databas
- Azure-prenumerationsöverföringar
- Azure Synapse Analytics
- Virtuella datorer (omfattar nu även åtkomst till minnesdumpar och hanterade diskar)

Om du vill aktivera Customer Lockbox för dessa förhands gransknings erbjudanden för din organisation kan du registrera dig för [Customer lockbox för offentlig för hands version av Azure](https://aka.ms/customerlockbox/insiderprogram)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Tjänster och scenarier som stöds i allmän tillgänglighet

Följande tjänster och scenarier är för närvarande allmänt tillgängliga för Customer Lockbox.

### <a name="remote-desktop-access-to-virtual-machines"></a>Fjärrskrivbordsåtkomst till virtuella datorer

Customer Lockbox är för närvarande aktiverat för begäranden om fjärrskrivbordsåtkomst till virtuella datorer. Följande arbetsbelastningar stöds:
- PaaS (plattform som en tjänst) – Azure Cloud Services (webbroll och arbetsroll)
- IaaS (infrastruktur som en tjänst) – Windows och Linux (endast Azure Resource Manager)
- Skaluppsättningar för virtuella datorer – Windows och Linux

> [!NOTE]
> IaaS klassiska instanser stöds inte av Customer Lockbox. Om du har arbets belastningar som körs på IaaS klassiska instanser rekommenderar vi att du migrerar dem från klassiska till Resource Manager-distributions modeller. Anvisningar finns i [Plattformsstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Detaljerade granskningsloggar

För scenarier som involverar fjärrskrivbordsåtkomst kan du använda Windows-händelseloggar för att granska de åtgärder som vidtagits av Microsoft-teknikern. Överväg att använda Azure Security Center för att samla in dina händelseloggar och kopiera data till din arbetsyta för analys. Mer information finns i [Datainsamling i Azure Security Center](../../security-center/security-center-enable-data-collection.md).

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

7. Vid kund organisationen får användaren som har [ägar rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) för Azure-prenumerationen ett e-postmeddelande från Microsoft för att meddela dem om den väntande åtkomst förfrågan. Den här personen är utsedd god kännare för Customer Lockbox begär Anden.

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

## <a name="auditing-logs"></a>Granskning av loggar

Customer Lockbox-loggar lagras i aktivitetsloggar. I Azure Portal väljer du **aktivitets loggar** för att Visa gransknings information som är relaterad till Customer lockbox begär Anden. Du kan filtrera på vissa åtgärder, till exempel:
- **Neka Lockbox-begäran**
- **Skapa Lockbox-begäran**
- **Godkänn Lockbox-begäran**
- **Förfallo datum för säker begäran**

Som exempel:

![Azure Customer Lockbox-aktivitets loggar](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>Customer Lockbox-integrering med Azure Security Benchmark

Vi har lanserat en ny bas linje kontroll ([3,13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) i Azures säkerhets benchmark som täcker Customer lockbox tillämplighet. Kunderna kan nu dra nytta av benchmark för att granska Customer Lockbox tillämpligheten för en tjänst.

## <a name="exclusions"></a>Undantag

Customer Lockbox-begäranden utlöses inte i följande scenarier för teknisk support:

- En Microsoft-tekniker måste utföra en aktivitet som ligger utanför standardprocedurerna för åtgärder. Till exempel att återställa tjänster i oväntade eller oförutsägbara scenarier.

- En Microsoft-tekniker får åtkomst till Azure-plattformen som en del av felsökning och får oavsiktligt åtkomst till kunddata. Till exempel kan Azures nätverksteam utföra felsökning som resulterar i en paketavbildning på en nätverksenhet. Men om kunden har krypterat data under överföringen, kan inte teknikern läsa data.

## <a name="next-steps"></a>Nästa steg

Customer Lockbox är automatiskt tillgängligt för alla kunder som har en [Support plan för Azure](https://azure.microsoft.com/support/plans/) med en minimal **utvecklings** nivå.

När du har en berättigad Support plan krävs ingen åtgärd för att aktivera Customer Lockbox. Customer Lockbox förfrågningar initieras av en Microsoft-tekniker om den här åtgärden krävs för att försätta ett support ärende som har arkiverats från någon i din organisation.