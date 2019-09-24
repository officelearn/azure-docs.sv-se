---
title: Beroende visualisering i Azure Migrate | Microsoft Docs
description: Innehåller en översikt över bedömnings beräkningar i Server Assessment service i Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: hamusa
ms.openlocfilehash: 33594e09778b9a629645e12357e6bafe561ad35e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202911"
---
# <a name="dependency-visualization"></a>Beroendevisualisering

Azure Migrate: Server utvärderingen bedömer grupper med lokala datorer för migrering till Azure. Du kan använda funktionen för beroende visualisering i Server utvärdering för att skapa grupper. Den här artikeln innehåller information om den här funktionen.

> [!NOTE]
> Funktionen för beroendevisualisering är inte tillgänglig i Azure Government.

## <a name="overview"></a>Översikt

Med beroende visualisering i Server utvärdering kan du skapa grupper med hög exakthet för utvärdering av migrering. Med hjälp av beroende visualisering kan du Visa nätverks beroenden för datorer och identifiera relaterade datorer som behöver migreras tillsammans till Azure. Den här funktionen är användbar i scenarier där du inte är helt medveten om de datorer som utgör ditt program och som måste migreras tillsammans till Azure.

## <a name="before-you-start"></a>Innan du börjar

- Se till att du har [skapat](how-to-add-tool-first-time.md) ett Azure Migrate-projekt.
- Om du redan har skapat ett projekt ser du till att du har [lagt till](how-to-assess.md) Azure Migrate: Verktyget Server bedömning.
- Kontrol lera att du har identifierat datorerna i Azure Migrate. Det kan du göra genom att konfigurera en Azure Migrate-enhet för [VMware](how-to-set-up-appliance-vmware.md) eller [Hyper-V](how-to-set-up-appliance-hyper-v.md). Enheten identifierar lokala datorer och skickar metadata-och prestanda data till Azure Migrate: Server Assessment. [Läs mer](migrate-appliance.md).

## <a name="how-does-it-work"></a>Hur fungerar det?

Azure Migrate använder [tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md) -lösningen i [Azure Monitor loggar](../log-analytics/log-analytics-overview.md) för beroende visualisering.
- Om du vill utnyttja beroende visualiseringen måste du associera en Log Analytics arbets yta, antingen ny eller befintlig, med ett Azure Migrate projekt.
- Du kan bara skapa eller koppla en arbets yta i samma prenumeration där Azure Migrate projektet skapas.
- Koppla en Log Analytics arbets yta till ett projekt:
    1. På fliken **servrar** i **Azure Migrate: Panelen Server** bedömning klickar du på **Översikt**.
    2. I **Översikt**klickar du på nedpilen för att expandera **grunderna**.
    3. I **OMS**-arbetsytan klickar du på **kräver konfiguration**.
    4. I **Konfigurera arbets yta**anger du om du vill skapa en ny arbets yta eller Använd en befintlig:
    
    ![Lägg till arbetsyta](./media/how-to-create-group-machine-dependencies/workspace.png)

- När du kopplar en arbets yta får du möjlighet att skapa en ny arbets yta eller att koppla en befintlig:
  - När du skapar en ny arbets yta måste du ange ett namn för arbets ytan. Du kan välja den [region](https://azure.microsoft.com/global-infrastructure/regions/) där arbets ytan ska skapas.
  - När du ansluter en befintlig arbets yta kan du välja mellan alla tillgängliga arbets ytor i samma prenumeration som migreringsjobbet. Observera att endast de arbets ytor som har skapats i en region där [tjänstkarta stöds](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites). Se till att du har "läsare"-åtkomst till arbets ytan för att kunna ansluta till en arbets yta.

  > [!NOTE]
  > När du har kopplat en arbets yta till ett projekt kan du inte ändra den senare.

  > [!NOTE]
  > Azure Migrate har för närvarande stöd för att skapa OMS-arbetsyta i regionerna östra USA, Sydostasien och Europa, västra. Om arbets ytan har skapats utanför Azure Migrate i någon annan region, kan den för närvarande inte associeras med ett Azure Migrate-projekt. 

- Den associerade arbets ytan är Taggad meddet här och värdet **projekt namn**, som du kan använda för att söka i Azure Portal.
- Om du vill navigera till arbets ytan som är kopplad till projektet kan du gå till avsnittet Essentials på projekt **översikts** sidan och komma åt arbets ytan

    ![Navigera Log Analytics arbets ytan](./media/concepts-dependency-visualization/oms-workspace.png)

Om du vill använda beroendevisualisering måste du ladda ned och installera agenter på varje lokal dator som du vill analysera.  

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) måste installeras på varje dator. [Läs mer](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) om hur du installerar MMA-agenten.
- [Beroende agenten](../azure-monitor/platform/agents-overview.md#dependency-agent) måste installeras på varje dator. [Läs mer](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) om hur du installerar beroende agenten.
- Om du har datorer utan Internetanslutning måste du dessutom ladda ned och installera Log Analytics-gatewayen på dem.

Du behöver inte dessa agenter på datorer som du vill utvärdera om du inte använder beroende visualisering.

## <a name="do-i-need-to-pay-for-it"></a>Behöver jag betala för det?

Funktionen för beroende visualisering är tillgänglig utan extra kostnad. Användning av funktionen beroende visualisering i Server utvärdering kräver Tjänstkarta och kräver att du associerar en Log Analytics arbets yta, antingen en ny eller befintlig, med Azure Migrate projektet. Funktionen för beroende visualisering i Server utvärdering är kostnads fri under de första 180 dagarna.

1. Om du använder andra lösningar än Tjänstkarta i den här Log Analytics arbets ytan debiteras [standard Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) avgifter.
2. Om du vill ha stöd för migrering utan extra kostnad debiteras Tjänstkarta-lösningen inga avgifter för de första 180 dagarna från dagen för att koppla arbets ytan Log Analytics till Azure Migrate-projektet. Efter 180 dagar kommer standard Log Analytics avgifter att gälla.

När du registrerar agenter på arbets ytan använder du det ID och den nyckel som angavs av projektet på sidan Installera agent-steg.

När Azure Migrate-projektet tas bort, tas inte arbets ytan bort tillsammans med den. Publicera projektet, Tjänstkarta användningen är inte kostnads fri och varje nod debiteras enligt den betalda nivån av Log Analytics arbets yta.

> [!NOTE]
> Funktionen för beroende visualisering använder Tjänstkarta via en Log Analytics arbets yta. Sedan den 28 februari 2018 med meddelandet om Azure Migrate allmän tillgänglighet är funktionen nu tillgänglig utan extra kostnad. Du måste skapa ett nytt projekt för att kunna använda arbets ytan ledig användning. Befintliga arbets ytor före allmän tillgänglighet är fortfarande debiterbara, och vi rekommenderar därför att du flyttar till ett nytt projekt.

Mer information om priser för Azure Migrate finns [här](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Hur gör jag för att hantera arbets ytan?

Du kan använda Log Analytics arbets ytan utanför Azure Migrate. Det tas inte bort om du tar bort Azure Migrate projektet där det skapades. Om du inte längre behöver arbets ytan [tar du bort den](../azure-monitor/platform/manage-access.md) manuellt.

Ta inte bort arbets ytan som skapats av Azure Migrate, om du inte tar bort Azure Migrate-projektet. Om du gör det fungerar inte beroende visualiserings funktionen som förväntat.

## <a name="next-steps"></a>Nästa steg
- [Gruppera datorer med dator beroenden](how-to-create-group-machine-dependencies.md)
- [Lär dig mer](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) om de vanliga frågorna om beroende visualisering.
