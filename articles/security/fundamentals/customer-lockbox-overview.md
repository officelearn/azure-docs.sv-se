---
title: Customer Lockbox för Microsoft Azure
description: Teknisk översikt över Customer Lockbox för Microsoft Azure, som ger kontroll över molnleverantörsåtkomst när Microsoft kan behöva komma åt kunddata.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: abc16ae7f7ab8bf15173248a6e7668e689e127de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561977"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Customer Lockbox för Microsoft Azure

> [!NOTE]
> Om du vill använda den här funktionen måste din organisation ha en [Azure-supportplan](https://azure.microsoft.com/support/plans/) med en minimal nivå av **utvecklare**.

Customer Lockbox för Microsoft Azure tillhandahåller ett gränssnitt där kunder kan granska och godkänna eller avvisa begäranden om åtkomst av kunddata. Den används i de fall där en Microsoft-tekniker behöver komma åt kunddata under en supportbegäran.

Den här artikeln beskriver hur Customer Lockbox-begäranden initieras, spåras och lagras för senare granskningar och granskningar.

Customer Lockbox är nu allmänt tillgänglig och för närvarande aktiverad för fjärrskrivbordsåtkomst till virtuella datorer.

## <a name="workflow"></a>Arbetsflöde

Följande steg beskriver ett typiskt arbetsflöde för en Customer Lockbox-begäran.

1. Någon på en organisation har problem med sin Azure-arbetsbelastning.

2. När den här personen har felsökt problemet, men inte kan åtgärda det, öppnar de en supportbiljett från [Azure-portalen](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Biljetten tilldelas en Azure-kundsupporttekniker.

3. En Azure Support Engineer granskar tjänstbegäran och bestämmer nästa steg för att lösa problemet.

4. Om supportteknikern inte kan felsöka problemet med hjälp av standardverktyg och telemetri är nästa steg att begära förhöjda behörigheter med hjälp av en JIT-åtkomsttjänst (Just-In-Time). Den här begäran kan vara från den ursprungliga supportteknikern. Eller så kan det vara från en annan tekniker eftersom problemet eskaleras till Azure DevOps-teamet.

5. När åtkomstbegäran har skickats av Azure Engineer utvärderar Just-In-Time-tjänsten begäran med beaktande av faktorer som:
    - Resursens omfattning
    - Om beställaren är en isolerad identitet eller om multifaktorautentisering
    - Behörighetsnivåer

    Baserat på jit-regeln kan den här begäran även innehålla ett godkännande från interna Microsoft-godkännare. Godkännaren kan till exempel vara kundsupport leadet eller DevOps Manager.

6. När begäran kräver direkt åtkomst till kunddata initieras en Customer Lockbox-begäran. Till exempel fjärrskrivbordsåtkomst till en kunds virtuella dator.

    Begäran är nu i ett **kundanmält** tillstånd och väntar på kundens godkännande innan du beviljar åtkomst.

7. På kundorganisationen får användaren som har [rollen Ägare](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) för Azure-prenumerationen ett e-postmeddelande från Microsoft för att meddela dem om den väntande åtkomstbegäran. För Customer Lockbox-begäranden är den här personen den utsedda godkännaren.

    Exempel på e-post:

    ![Azure Customer Lockbox – e-postmeddelande](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. E-postmeddelandet innehåller en länk till **customer lockbox-bladet** i Azure-portalen. Med den här länken loggar den utsedda godkännaren in på Azure-portalen för att visa alla väntande begäranden som deras organisation har för Customer Lockbox:

    ![Azure Customer Lockbox - målsida](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   Begäran finns kvar i kundkön i fyra dagar. Efter den här tiden upphör åtkomstbegäran automatiskt och ingen åtkomst beviljas Microsoft-tekniker.

9. Om du vill hämta information om den väntande begäran kan den utsedda godkännaren välja lockbox-begäran från **Väntande begäranden:**

    ![Azure Customer Lockbox – visa den väntande begäran](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. Den utsedda godkännaren kan också välja **SERVICE REQUEST ID** för att visa supportbiljettbegäran som skapades av den ursprungliga användaren. Den här informationen ger sammanhang för varför Microsoft Support är aktiverat och historiken för det rapporterade problemet. Ett exempel:

    ![Azure Customer Lockbox – visa begäran om supportbiljett](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. När du har granskat begäran väljer den utsedda godkännaren **Godkänn** eller **Neka:**

    ![Azure Customer Lockbox – välj Godkänn eller neka](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    Som ett resultat av urvalet:
    - **Godkänn**: Åtkomst beviljas Microsoft-teknikern. Åtkomsten beviljas för en standardperiod på åtta timmar.
    - **Neka**: Microsoft-teknikerns begäran om förhöjd åtkomst avvisas och inga ytterligare åtgärder vidtas.

I granskningssyfte loggas de åtgärder som vidtas i det här arbetsflödet i [kundlåsboxbegäransloggar](#auditing-logs).

## <a name="auditing-logs"></a>Granskningsloggar

Customer Lockbox-loggar lagras i aktivitetsloggar. I Azure-portalen väljer du **Aktivitetsloggar** för att visa granskningsinformation relaterad till Customer Lockbox-begäranden. Du kan filtrera efter specifika åtgärder, till exempel:
- **Neka Lockbox-begäran**
- **Skapa begäran om låsbox**
- **Godkänn Lockbox-begäran**
- **Upphör att gälla för Lockbox-begäran**

Som exempel:

![Azure Customer Lockbox - aktivitetsloggar](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Tjänster och scenarier som stöds i allmän tillgänglighet

Följande tjänster och scenarier är för närvarande i allmän tillgänglighet för Customer Lockbox.

### <a name="remote-desktop-access-to-virtual-machines"></a>Åtkomst till fjärrskrivbord till virtuella datorer

Customer Lockbox är för närvarande aktiverat för åtkomstbegäranden för fjärrskrivbord till virtuella datorer. Följande arbetsbelastningar stöds:
- Plattform som en tjänst (PaaS) - Azure Cloud Services (webbroll och arbetsroll)
- Infrastruktur som en tjänst (IaaS) – Windows och Linux (endast Azure Resource Manager)
- Skaluppsättning för virtuella datorer - Windows och Linux

> [!NOTE]
> IaaS Classic-instanser stöds inte av Customer Lockbox. Om du har arbetsbelastningar som körs på IaaS Classic-instanser rekommenderar vi att du migrerar dem från distributionsmodeller för Klassisk till Resource Manager. Instruktioner finns i [Migrering av IaaS-resurser som stöds av plattformen från klassisk till Azure Resource Manager](../../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Detaljerade granskningsloggar

För scenarier som innebär fjärrskrivbordsåtkomst kan du använda Windows-händelseloggar för att granska de åtgärder som vidtagits av Microsoft-teknikern. Överväg att använda Azure Security Center för att samla in dina händelseloggar och kopiera data till din arbetsyta för analys. Mer information finns [i Datainsamling i Azure Security Center](../../security-center/security-center-enable-data-collection.md).

## <a name="supported-services-and-scenarios-in-preview"></a>Tjänster och scenarier som stöds i förhandsversion

Följande tjänster är nu förhandsgranskning för Customer Lockbox:

- Azure Storage

- Azure SQL-databas

- Azure-datautforskaren

- Virtuella datorer (som nu även täcker åtkomst till minnesdumpar och hanterade diskar)

- Överföringar av Azure-prenumerationer

Om du vill aktivera Customer Lockbox för dessa förhandsversioner för din organisation registrerar du dig för [Customer Lockbox för Azure Public Preview](https://aka.ms/customerlockbox/insiderprogram).


## <a name="exclusions"></a>Undantag

Customer Lockbox-begäranden utlöses inte i följande scenarier för teknisk support:

- En Microsoft-tekniker måste göra en aktivitet som inte omfattas av standardrutiner. Om du till exempel vill återställa eller återställa tjänster i oväntade eller oförutsägbara scenarier.

- En Microsoft-tekniker kommer åt Azure-plattformen som en del av felsökningen och har av misstag åtkomst till kunddata. Azure Network Team utför till exempel felsökning som resulterar i en paketfångst på en nätverksenhet. Men om kunden krypterade data under överföringen kan teknikern inte läsa data.

## <a name="next-steps"></a>Nästa steg

Customer Lockbox är automatiskt tillgängligt för alla kunder som har en [Azure-supportplan](https://azure.microsoft.com/support/plans/) med en minimal nivå av **utvecklare**.

När du har en kvalificerad supportplan krävs ingen åtgärd av dig för att aktivera Customer Lockbox. Customer Lockbox-begäranden initieras av en Microsoft-tekniker om den här åtgärden behövs för att föra en supportbiljett som har arkiverats från någon i organisationen.
