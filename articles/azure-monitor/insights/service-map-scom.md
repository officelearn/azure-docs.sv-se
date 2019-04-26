---
title: Tjänstkarta-integrering med System Center Operations Manager | Microsoft Docs
description: Tjänstkarta är en lösning i Azure som automatiskt identifierar programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Den här artikeln beskrivs med hjälp av Tjänstkarta för att automatiskt skapa diagram över distribuerade program i Operations Manager.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: magoedte
ms.openlocfilehash: 40e6d6ff6ea8748b525642e5507c80590b322b7a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402620"
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Tjänstkarta-integrering med System Center Operations Manager

Tjänstkarta identifierar automatiskt programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Tjänstkarta kan du visa dina servrar på sätt som du tänker på dem, sammankopplat system som levererar viktiga tjänster. Tjänstkarta visar anslutningar mellan servrar, processer och portar i alla TCP-anslutna arkitekturer utan konfiguration krävs förutom installationen av en agent. Mer information finns i den [Tjänstkarta dokumentation]( service-map.md).

Med den här integreringen mellan Service Map och System Center Operations Manager kan du automatiskt skapa diagram över distribuerade program i Operations Manager som är baserade på dynamiska beroende-kartor i Tjänstkartan.

## <a name="prerequisites"></a>Nödvändiga komponenter
* En Operations Manager-hanteringsgrupp (2012 R2 eller senare) som hanterar en uppsättning servrar.
* Log Analytics-arbetsytan med lösningen Tjänstkarta aktiverat.
* En uppsättning servrar (minst) som hanteras av Operations Manager och skicka data till Service Map. Windows och Linux-servrar stöds.
* Ett huvudnamn för tjänsten med åtkomst till Azure-prenumerationen som är associerad med Log Analytics-arbetsytan. Mer information går du till [skapa ett huvudnamn för tjänsten](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Installera Service Map management pack
Du kan aktivera integrering mellan Operations Manager och Service Map genom att importera Microsoft.SystemCenter.ServiceMap hanteringspaketsamlingen (Microsoft.SystemCenter.ServiceMap.mpb). Du kan hämta hanteringspaketsamling från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). Paketet innehåller följande hanteringspaket:
* Microsoft Service Map Programvyer
* Microsoft System Center Service Map internt
* Microsoft System Center Service Map åsidosättningar
* Microsoft System Center Service Map

## <a name="configure-the-service-map-integration"></a>Konfigurera Service Map-integrering
När du har installerat en ny nod i hanteringspaketet Tjänstkarta **Tjänstkarta**, visas under **Operations Management Suite** i den **Administration** fönstret.

>[!NOTE]
>[Operations Management Suite har en samling tjänster](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) som finns med Log Analytics, vilket är nu en del av [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Om du vill konfigurera Service Map integration, gör du följande:

1. Öppna guiden Konfigurera i den **kartan tjänstöversikt** fönstret klickar du på **Lägg till arbetsyta**.  

    ![Översiktsfönstret för Service Map](media/service-map-scom/scom-configuration.png)

2. I den **anslutningskonfigurationen** , ange klientorganisationens namn eller ID, program-ID (även kallat användarnamn eller clientID) och lösenordet för tjänstens huvudnamn och klicka sedan på **nästa**. Mer information går du till att skapa ett huvudnamn för tjänsten.

    ![Fönstret anslutningskonfiguration](media/service-map-scom/scom-config-spn.png)

3. I den **val av prenumeration** fönstret Välj Azure-prenumeration, Azure-resursgrupp (det som innehåller Log Analytics-arbetsytan) och Log Analytics-arbetsytan och klicka sedan på **nästa**.

    ![Operations Manager-konfiguration-arbetsyta](media/service-map-scom/scom-config-workspace.png)

4. I den **val av dator** fönstret väljer vilka kartan datorgrupper som du vill synkronisera till Operations Manager. Klicka på **Lägg till/ta bort datorgrupper**, Välj grupper i listan över **tillgängliga datorgrupper**, och klicka på **Lägg till**.  När du har valt grupper klickar du på **Ok** ska slutföras.

    ![I Operations Manager Configuration datorgrupper](media/service-map-scom/scom-config-machine-groups.png)

5. I den **Serverval** fönstret Konfigurera du gruppen Service Map servrar med de servrar som du vill synkronisera mellan Operations Manager och Service Map. Klicka på **Lägg till/ta bort servrar**.   

    Servern för att integrationen ska skapa ett diagram över distribuerade program för en server, måste vara:

   * Hanteras av Operations Manager
   * Hanteras av Tjänstkarta
   * Visas i gruppen Service Map-servrar

     ![Gruppen för Operations Manager-konfiguration](media/service-map-scom/scom-config-group.png)

6. Valfritt: Välj resurspool Management-servern kan kommunicera med Log Analytics och klickar sedan på **lägger du till arbetsytan**.

    ![Operations Manager Configuration resurspoolen](media/service-map-scom/scom-config-pool.png)

    Det kan ta några minuter och konfigurera och registrera Log Analytics-arbetsytan. När den har konfigurerats, initierar den första synkroniseringen i Service Map Operations Manager.

    ![Operations Manager Configuration resurspoolen](media/service-map-scom/scom-config-success.png)


## <a name="monitor-service-map"></a>Övervaka Service Map
När Log Analytics-arbetsytan är ansluten, visas en ny mapp Tjänstkartan i den **övervakning** rutan i Operations Manager-konsolen.

![Fönstret Operations Manager-övervakning](media/service-map-scom/scom-monitoring.png)

Mappen Tjänstkarta har fyra noder:
* **Aktiva aviseringar**: Visar en lista över alla aktiva aviseringar om kommunikationen mellan Operations Manager och Service Map.  Observera att dessa aviseringar inte är Log Analytics aviseringar som synkroniseras till Operations Manager.

* **Servrar**: Visar en lista över de övervakade servrarna som är konfigurerade att synkronisera från Tjänstkarta.

    ![Övervakning av servrar i Operations Manager-fönstret](media/service-map-scom/scom-monitoring-servers.png)

* **Datorn beroende Gruppvyer**: Visar en lista över alla datorgrupper som synkroniseras från Tjänstkarta. Du kan klicka på valfri grupp om du vill visa dess diagram över distribuerade program.

    ![Diagram för Operations Manager-distribuerade program](media/service-map-scom/scom-group-dad.png)

* **Servern beroende vyer**: Visar alla servrar som synkroniseras från Tjänstkarta. Du kan klicka på valfri server om du vill visa dess diagram över distribuerade program.

    ![Diagram för Operations Manager-distribuerade program](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Redigera eller ta bort arbetsytan
Du kan redigera eller ta bort den konfigurerade arbetsytan via den **kartan tjänstöversikt** fönstret (**Administration** fönstret > **Operations Management Suite**  >  **Service Map**).

>[!NOTE]
>[Operations Management Suite har en samling tjänster](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) som finns med Log Analytics, vilket är nu en del av [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Du kan konfigurera endast en Log Analytics-arbetsyta för tillfället.

![Fönstret Redigera arbetsyta för Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurera regler och åsidosättningar
En regel _Microsoft.SystemCenter.ServiceMapImport.Rule_, har skapats för att regelbundet hämta information från Tjänstkarta. Om du vill ändra synkronisering tidsinställningar, du kan konfigurera åsidosättningar av regeln (**redigering** fönstret > **regler** > **Microsoft.SystemCenter.ServiceMapImport.Rule**) .

![Egenskapsfönstret åsidosättningar i Operations Manager](media/service-map-scom/scom-overrides.png)

* **Aktiverad**: Aktivera eller inaktivera automatiska uppdateringar.
* **IntervalMinutes**: Återställa tiden mellan uppdateringar. Standardintervallet är en timme. Om du vill synkronisera server maps oftare, ändrar du värdet.
* **TimeoutSeconds**: Återställa hur lång tid innan tidsgränsen uppnås för begäran.
* **TimeWindowMinutes**: Återställa tidsperioden för att köra frågor mot data. Standardvärdet är 60 minuter-fönstret. Det maximala värdet som tillåts av Service Map är 60 minuter.

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

Den aktuella designen visar följande problem och begränsningar:
* Du kan bara ansluta till en enda Log Analytics-arbetsyta.
* Du kan lägga till servrar i kartan servrar tjänstgruppen manuellt via den **redigering** fönstret maps för servrarna synkroniseras inte omedelbart.  De kommer att synkroniseras från Service Map under nästa synkroniseringscykel.
* Om du ändrar i diagram för distribuerade program som skapats av management pack ändringarna kommer sannolikt att skrivas över vid nästa synkronisering med Service Map.

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten
Officiella Azure dokumentation om hur du skapar en tjänst huvudnamn finns:
* [Skapa ett huvudnamn för tjänsten med hjälp av PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Skapa ett huvudnamn för tjänsten med hjälp av Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Skapa ett huvudnamn för tjänsten med hjälp av Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Feedback
Har du feedback för oss om Tjänstkarta eller den här dokumentationen? Besök vår [User Voice-sidan](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), där du kan föreslå funktioner eller rösta på förslag på befintliga.
