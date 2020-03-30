---
title: Integrera Azure Monitor för virtuella datorer karta med Operations Manager | Microsoft-dokument
description: Azure Monitor för virtuella datorer identifierar automatiskt programkomponenter på Windows- och Linux-system och mappar kommunikationen mellan tjänster. I den här artikeln beskrivs hur du använder kartfunktionen för att automatiskt skapa distribuerade programdiagram i Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 112413720f969474369555a74bc89846666e2ef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663461"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>Funktionen Integrera System Center Operations Manager med Azure Monitor för virtuella datorer Kartfunktion

I Azure Monitor för virtuella datorer kan du visa identifierade programkomponenter på Windows och Virtuella Linux-datorer (VMs) som körs i Azure eller din miljö. Med den här integreringen mellan kartfunktionen och System Center Operations Manager kan du automatiskt skapa distribuerade programdiagram i Operations Manager som baseras på de dynamiska beroendemappningarna i Azure Monitor för virtuella datorer. I den här artikeln beskrivs hur du konfigurerar hanteringsgruppen systemcenter operationshanteraren för att stödja den här funktionen.

>[!NOTE]
>Om du redan har distribuerat Service Map kan du visa dina kartor i Azure Monitor för virtuella datorer, som innehåller ytterligare funktioner för att övervaka vm-hälsa och prestanda. Kartfunktionen i Azure Monitor för virtuella datorer är avsedd att ersätta den fristående servicemappningslösningen. Mer information finns i [översikt över Azure Monitor för virtuella datorer](vminsights-overview.md).

## <a name="prerequisites"></a>Krav

* En hanteringsgrupp för System Center Operations Manager (2012 R2 eller senare).
* En Log Analytics-arbetsyta som konfigurerats för att stödja Azure Monitor för virtuella datorer.
* En eller flera virtuella Windows- och Linux-datorer eller fysiska datorer som övervakas av Operations Manager och skickar data till logganalysarbetsytan. Linux-servrar som rapporterar till en Hanteringsgrupp för Operations Manager måste konfigureras för att ansluta direkt till Azure Monitor. Mer information finns i översikten i [Samla in loggdata med Log Analytics-agenten](../platform/log-analytics-agent.md).
* Ett tjänsthuvudnamn med åtkomst till Azure-prenumerationen som är associerad med Log Analytics-arbetsytan. Mer information finns i [Skapa ett huvudnamn för tjänsten](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Installera hanteringspaketet för servicekart

Du aktiverar integreringen mellan Operations Manager och kartfunktionen genom att importera hanteringspaketet Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Du kan hämta hanteringspaketet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). Paketet innehåller följande hanteringspaket:

* Programvyer för Microsoft-tjänstmappning
* Microsoft System Center Service Map Intern
* Åsidosättningar i Tjänstordningsmappningar för Microsoft System Center
* Serviceöversikt i Microsoft System Center

## <a name="configure-integration"></a>Konfigurera integrering

När du har installerat hanteringspaketet för Service Map visas en ny nod, **Service Map,** under **Operations Management Suite** i **administrationsfönstret** på Operations Manager Operations-konsolen.

>[!NOTE]
>[Operations Management Suite var en samling tjänster](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) som inkluderade Log Analytics, är nu en del av Azure [Monitor](../overview.md).

Så här konfigurerar du Azure Monitor för mappningsmappningsintegrering:

1. Om du vill öppna konfigurationsguiden klickar du på **Lägg till arbetsyta**i fönstret Översikt över **tjänstmappning** .  

    ![Översiktsfönstret För servicemappning](media/service-map-scom/scom-configuration.png)

2. I fönstret **Anslutningskonfiguration** anger du klientnamnet eller id-d.v.s. program-ID(kallas även användarnamnet eller klient-ID)-namnet och klickar sedan på **Nästa**. Mer information finns i Skapa ett huvudnamn för tjänsten.

    ![Fönstret Anslutningskonfiguration](media/service-map-scom/scom-config-spn.png)

3. I fönstret **Prenumerationsval** väljer du Azure-prenumerationen, Azure-resursgruppen (den som innehåller log analytics-arbetsytan) och Log Analytics-arbetsytan och klickar sedan på **Nästa**.

    ![Konfigurationsarbetsyta för Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. I fönstret **Val av maskingrupp** väljer du vilka servicemappningsmaskingrupper som du vill synkronisera med Operations Manager. Klicka på **Lägg till/ta bort maskingrupper,** välj grupper i listan över **tillgängliga datorgrupper**och klicka på **Lägg till**.  När du är klar med att välja grupper klickar du på **Ok** för att avsluta.

    ![Konfigurationsmaskingrupper för Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. I fönstret **Serverval** konfigurerar du servicemappningsservergruppen med de servrar som du vill synkronisera mellan Operations Manager och kartfunktionen. Klicka på **Lägg till/ta bort servrar**.

    För att integreringen ska kunna skapa ett distribuerat programdiagram för en server måste servern vara:

   * Övervakas av Operations Manager
   * Konfigurerad för att rapportera till arbetsytan Log Analytics som konfigurerats med Azure Monitor för virtuella datorer
   * Listad i servicemappningsservergruppen

     ![Konfigurationsgruppen för Operations Manager](media/service-map-scom/scom-config-group.png)

6. Valfritt: Välj resurspoolen Alla hanteringsservrar för att kommunicera med Logganalys och klicka sedan på **Lägg till arbetsyta**.

    ![Konfigurationsresurspoolen för Operations Manager](media/service-map-scom/scom-config-pool.png)

    Det kan ta en minut att konfigurera och registrera log analytics-arbetsytan. När den har konfigurerats initierar Operations Manager den första kartsynkroniseringen.

    ![Konfigurationsresurspoolen för Operations Manager](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Övervaka integrering

När arbetsytan Log Analytics har anslutits visas en ny mapp, Service Map, i **övervakningsfönstret** på Operations Manager Operations-konsolen.

![Övervakningsfönstret för Operations Manager](media/service-map-scom/scom-monitoring.png)

Mappen Service map har fyra noder:

* **Aktiva aviseringar**: Visar alla aktiva aviseringar om kommunikationen mellan Operations Manager och Azure Monitor.  

  >[!NOTE]
  >Dessa aviseringar är inte Log Analytics-aviseringar synkroniserade med Operations Manager, de genereras i hanteringsgruppen baserat på arbetsflöden som definierats i servicemappningshanteringspaketet.

* **Servrar**: Visar en lista över övervakade servrar som är konfigurerade för synkronisering från Azure Monitor för virtuella datorer Kartfunktionen.

    ![Fönstret Övervakningsservrar för Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **Beroendevyer för maskingrupp:** Visar en lista över alla datorgrupper som synkroniseras från kartfunktionen. Du kan klicka på en grupp om du vill visa det distribuerade programdiagrammet.

    ![Operations Manager distribuerade programdiagram](media/service-map-scom/scom-group-dad.png)

* **Serverberoendevyer**: Visar en lista över alla servrar som synkroniseras från kartfunktionen. Du kan klicka på valfri server för att visa dess distribuerade programdiagram.

    ![Operations Manager distribuerade programdiagram](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Redigera eller ta bort arbetsytan

Du kan redigera eller ta bort den konfigurerade arbetsytan via **översiktsfönstret För servicemappning** (**administrationsfönstret** > Service Map för Operations **Management** > **Suite**).

>[!NOTE]
>[Operations Management Suite var en samling tjänster](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) som inkluderade Log Analytics, som nu ingår i Azure [Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Du kan bara konfigurera en Log Analytics-arbetsyta i den aktuella versionen.

![Fönstret Redigera arbetsyta i OperationsHanteraren](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurera regler och åsidosättningar

En regel, *Microsoft.SystemCenter.ServiceMapImport.Rule*, hämtar regelbundet information från Azure Monitor för virtuella datorer Kartfunktion. Om du vill ändra synkroniseringsintervallet kan du åsidosätta regeln och ändra värdet för parametern **IntervalMinutes**.

![Fönstret Egenskaper för OperationsHanteraren åsidosätter](media/service-map-scom/scom-overrides.png)

* **Aktiverad**: Aktivera eller inaktivera automatiska uppdateringar.
* **IntervallMinuter**: Anger tiden mellan uppdateringarna. Standardintervallet är en timme. Om du vill synkronisera kartor oftare kan du ändra värdet.
* **TimeoutSeconds**: Anger hur lång tid som innan begäran timeout.
* **TimeWindowMinutes**: Anger tidsfönstret för att fråga data. Standardvärdet är 60 minuter, vilket är det högsta tillåtna intervallet.

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

Den aktuella designen presenterar följande frågor och begränsningar:

* Du kan bara ansluta till en enda Log Analytics-arbetsyta.
* Även om du kan lägga till servrar i servicemappningsservergruppen manuellt via **redigeringsfönstret** synkroniseras inte kartorna för dessa servrar omedelbart. De synkroniseras från Azure Monitor för virtuella datorer Karta funktionen under nästa synkroniseringscykel.
* Om du gör några ändringar i de distribuerade programdiagram som skapats av hanteringspaketet skrivs dessa ändringar troligen över vid nästa synkronisering med Azure Monitor för virtuella datorer.

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Officiella Azure-dokumentation om hur du skapar ett huvudnamn för tjänsten finns i:

* [Skapa ett huvudnamn för tjänsten med PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Skapa ett huvudnamn för tjänsten med hjälp av Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Skapa ett huvudnamn för tjänsten med hjälp av Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Feedback
Har du någon feedback till oss om integrering med Azure Monitor för virtuella datorer Kartfunktion eller den här dokumentationen? Besök vår [User Voice-sida,](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)där du kan föreslå funktioner eller rösta på befintliga förslag.
