---
title: Tjänstkarta-integrering med System Center Operations Manager | Microsoft Docs
description: Tjänstkarta är en lösning i Azure som automatiskt identifierar programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Den här artikeln beskrivs med hjälp av Tjänstkarta för att automatiskt skapa diagram över distribuerade program i Operations Manager.
services: monitoring
documentationcenter: ''
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.openlocfilehash: 6fc6afa9c0ccbddcfa408556dee92618fe63c8fb
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407120"
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Tjänstkarta-integrering med System Center Operations Manager
  > [!NOTE]
  > Den här funktionen är i offentlig förhandsversion.
  > 
  
Tjänstkarta identifierar automatiskt programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Tjänstkarta kan du visa dina servrar på sätt som du tänker på dem, sammankopplat system som levererar viktiga tjänster. Tjänstkarta visar anslutningar mellan servrar, processer och portar i alla TCP-anslutna arkitekturer utan konfiguration krävs förutom installationen av en agent. Mer information finns i den [Tjänstkarta dokumentation]( monitoring-service-map.md).

Med den här integreringen mellan Service Map och System Center Operations Manager kan du automatiskt skapa diagram över distribuerade program i Operations Manager som är baserade på dynamiska beroende-kartor i Tjänstkartan.

## <a name="prerequisites"></a>Förutsättningar
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

    ![Översiktsfönstret för Service Map](media/monitoring-service-map/scom-configuration.png)

2. I den **anslutningskonfigurationen** , ange klientorganisationens namn eller ID, program-ID (även kallat användarnamn eller clientID) och lösenordet för tjänstens huvudnamn och klicka sedan på **nästa**. Mer information går du till [skapa ett huvudnamn för tjänsten](#creating-a-service-principal).

    ![Fönstret anslutningskonfiguration](media/monitoring-service-map/scom-config-spn.png)

3. I den **val av prenumeration** fönstret Välj Azure-prenumeration, Azure-resursgrupp (det som innehåller Log Analytics-arbetsytan) och Log Analytics-arbetsytan och klicka sedan på **nästa**.

    ![Operations Manager-konfiguration-arbetsyta](media/monitoring-service-map/scom-config-workspace.png)

4. I den **val av dator** fönstret väljer vilka kartan datorgrupper som du vill synkronisera till Operations Manager. Klicka på **Lägg till/ta bort datorgrupper**, Välj grupper i listan över **tillgängliga datorgrupper**, och klicka på **Lägg till**.  När du har valt grupper klickar du på **Ok** ska slutföras.
    
    ![I Operations Manager Configuration datorgrupper](media/monitoring-service-map/scom-config-machine-groups.png)
    
5. I den **Serverval** fönstret Konfigurera du gruppen Service Map servrar med de servrar som du vill synkronisera mellan Operations Manager och Service Map. Klicka på **Lägg till/ta bort servrar**.   
    
    Servern för att integrationen ska skapa ett diagram över distribuerade program för en server, måste vara:

    * Hanteras av Operations Manager
    * Hanteras av Tjänstkarta
    * Visas i gruppen Service Map-servrar

    ![Gruppen för Operations Manager-konfiguration](media/monitoring-service-map/scom-config-group.png)

6. Valfritt: Välj resurspoolen för hanteringsservern att kommunicera med Log Analytics och klicka sedan på **lägger du till arbetsytan**.

    ![Operations Manager Configuration resurspoolen](media/monitoring-service-map/scom-config-pool.png)

    Det kan ta några minuter och konfigurera och registrera Log Analytics-arbetsytan. När den har konfigurerats, initierar den första synkroniseringen i Service Map Operations Manager.

    ![Operations Manager Configuration resurspoolen](media/monitoring-service-map/scom-config-success.png)


## <a name="monitor-service-map"></a>Övervaka Service Map
När Log Analytics-arbetsytan är ansluten, visas en ny mapp Tjänstkartan i den **övervakning** rutan i Operations Manager-konsolen.

![Fönstret Operations Manager-övervakning](media/monitoring-service-map/scom-monitoring.png)

Mappen Tjänstkarta har fyra noder:
* **Aktiva aviseringar**: Visar en lista över alla aktiva aviseringar om kommunikationen mellan Operations Manager och Service Map.  Observera att dessa aviseringar inte är Log Analytics aviseringar som synkroniseras till Operations Manager. 

* **Servrar**: Visar en lista över de övervakade servrarna som är konfigurerade att synkronisera från Tjänstkarta.

    ![Övervakning av servrar i Operations Manager-fönstret](media/monitoring-service-map/scom-monitoring-servers.png)

* **Datorn beroende Gruppvyer**: Visar en lista över alla datorgrupper som synkroniseras från Tjänstkarta. Du kan klicka på valfri grupp om du vill visa dess diagram över distribuerade program.

    ![Diagram för Operations Manager-distribuerade program](media/monitoring-service-map/scom-group-dad.png)

* **Servern beroende vyer**: Visar en lista över alla servrar som synkroniseras från Tjänstkarta. Du kan klicka på valfri server om du vill visa dess diagram över distribuerade program.

    ![Diagram för Operations Manager-distribuerade program](media/monitoring-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Redigera eller ta bort arbetsytan
Du kan redigera eller ta bort den konfigurerade arbetsytan via den **kartan tjänstöversikt** fönstret (**Administration** fönstret > **Operations Management Suite**  >  **Service Map**).

>[!NOTE]
>[Operations Management Suite har en samling tjänster](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) som finns med Log Analytics, vilket är nu en del av [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Du kan konfigurera endast en Log Analytics-arbetsyta för tillfället.

![Fönstret Redigera arbetsyta för Operations Manager](media/monitoring-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurera regler och åsidosättningar
En regel _Microsoft.SystemCenter.ServiceMapImport.Rule_, har skapats för att regelbundet hämta information från Tjänstkarta. Om du vill ändra synkronisering tidsinställningar, du kan konfigurera åsidosättningar av regeln (**redigering** fönstret > **regler** > **Microsoft.SystemCenter.ServiceMapImport.Rule**) .

![Egenskapsfönstret åsidosättningar i Operations Manager](media/monitoring-service-map/scom-overrides.png)

* **Aktiverad**: aktivera eller inaktivera automatiska uppdateringar. 
* **IntervalMinutes**: Återställ tiden mellan uppdateringar. Standardintervallet är en timme. Om du vill synkronisera server maps oftare, ändrar du värdet.
* **TimeoutSeconds**: återställa hur lång tid innan tidsgränsen uppnås för begäran. 
* **TimeWindowMinutes**: återställa tidsperioden för att köra frågor mot data. Standardvärdet är 60 minuter-fönstret. Det maximala värdet som tillåts av Service Map är 60 minuter.

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
