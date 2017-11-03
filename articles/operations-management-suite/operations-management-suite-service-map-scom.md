---
title: "Tjänstkarta integrering med System Center Operations Manager | Microsoft Docs"
description: "Tjänstkarta är en Operations Management Suite-lösning som automatiskt identifierar programkomponenter på Windows- och Linux-system och mappar kommunikationen mellan tjänster. Den här artikeln beskrivs med hjälp av en Tjänstkarta att automatiskt skapa diagram för distribuerade program i Operations Manager."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.openlocfilehash: fb58a01828e13f9605e4788ee7e064162a7a31d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Tjänstkarta integrering med System Center Operations Manager
  > [!NOTE]
  > Den här funktionen är tillgänglig som förhandsversion.
  > 
  
Operations Management Suite Tjänstkarta automatiskt identifierar programkomponenter på Windows- och Linux-system och mappar kommunikationen mellan tjänster. Tjänstkarta kan du visa dina servrar på sätt som du betrakta dem som sammanlänkade system som levererar kritiska tjänster. Tjänstkarta visar anslutningar mellan servrar, processer och portar i alla TCP-anslutna arkitektur med ingen konfiguration krävs förutom installation av en agent. Mer information finns i [Tjänstkarta dokumentationen](operations-management-suite-service-map.md).

Med den här integreringen mellan Tjänstkarta och System Center Operations Manager kan automatiskt skapa diagram för distribuerade program i Operations Manager som är baserade på dynamiskt beroende maps i Tjänstkartan.

## <a name="prerequisites"></a>Krav
* En Operations Manager-hanteringsgrupp som hanterar en uppsättning servrar.
* En Operations Management Suite-arbetsyta med Tjänstkarta lösningen aktiverad.
* En uppsättning servrar (minst) som hanteras av Operations Manager och skicka data till Tjänstkartan. Windows- och Linux-servrar stöds.
* Ett huvudnamn för tjänsten med åtkomst till Azure-prenumerationen som är associerad med Operations Management Suite-arbetsyta. Mer information finns på [skapa ett huvudnamn för tjänsten](#creating-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Installera Tjänstkarta management pack
Du kan aktivera integrering mellan Operations Manager och Tjänstkarta genom att importera det Microsoft.SystemCenter.ServiceMap management Pack (Microsoft.SystemCenter.ServiceMap.mpb). Du kan ladda ned det management Pack från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). Paketet innehåller följande hanteringspaket:
* Microsoft Service kartvy program
* Microsoft System Center Service Map internt
* Microsoft System Center Service Map åsidosättningar
* Microsoft System Center-Tjänstkarta

## <a name="configure-the-service-map-integration"></a>Konfigurera Tjänstkarta-integrering
När du har installerat en ny nod i hanteringspaketet Tjänstkarta **Tjänstkarta**, visas under **Operations Management Suite** i den **Administration** fönstret. 

Om du vill konfigurera Tjänstkarta-integrering gör du följande:

1. Öppna guiden Konfigurera i den **översikt över tjänsten** rutan klickar du på **lägger du till arbetsytan**.  

    ![Översikt över tjänsten fönstret](media/oms-service-map/scom-configuration.png)

2. I den **anslutningskonfiguration** , ange innehavarens namn eller ID, program-ID (användarnamn eller clientID) och lösenordet för tjänstens huvudnamn och klicka sedan på **nästa**. Mer information finns på [skapa ett huvudnamn för tjänsten](#creating-a-service-principal).

    ![Fönstret anslutningskonfiguration](media/oms-service-map/scom-config-spn.png)

3. I den **prenumeration markeringen** fönstret Välj Azure-prenumeration, Azure-resursgrupp (en som innehåller Operations Management Suite-arbetsyta) och Operations Management Suite-arbetsyta och klicka sedan på **Nästa**.

    ![Operations Manager Configuration-arbetsyta](media/oms-service-map/scom-config-workspace.png)

4. I den **val av datorn** fönstret väljer vilka kartan datorgrupper som du vill synkronisera till Operations Manager. Klicka på **Lägg till/ta bort datorgrupper**, Välj grupper i listan över **tillgängliga datorgrupper**, och klicka på **Lägg till**.  När du har valt grupper klickar du på **Ok** ska slutföras.
    
    ![Operations Manager Configuration dator grupper](media/oms-service-map/scom-config-machine-groups.png)
    
5. I den **Serverval** fönstret du konfigurerar gruppen Service Map servrar med de servrar som du vill synkronisera mellan Operations Manager och Tjänstkartan. Klicka på **Lägg till/ta bort servrar**.   
    
    Servern för integrering skapa ett distribuerat program diagram för en server måste vara:

    * Hanteras av Operations Manager
    * Hanteras av en Tjänstkarta
    * Som anges i gruppen Service Map servrar

    ![Gruppen för Operations Manager-konfiguration](media/oms-service-map/scom-config-group.png)

6. Valfritt: Välj resurspoolen hanteringsservern kan kommunicera med Operations Management Suite och klicka sedan på **lägger du till arbetsytan**.

    ![Operations Manager Configuration resurspoolen](media/oms-service-map/scom-config-pool.png)

    Det kan ta någon minut att konfigurera och registrera Operations Management Suite-arbetsyta. När den har konfigurerats, initierar den första synkroniseringen Tjänstkarta från Operations Management Suite Operations Manager.

    ![Operations Manager Configuration resurspoolen](media/oms-service-map/scom-config-success.png)


## <a name="monitor-service-map"></a>Övervakare för Tjänstmappning
När Operations Management Suite-arbetsyta är anslutet visas en ny mapp på Tjänstkartan, i den **övervakning** rutan i Operations Manager-konsolen.

![Övervakning av Operations Manager-fönstret](media/oms-service-map/scom-monitoring.png)

Mappen Tjänstkarta har fyra noder:
* **Aktiva aviseringar**: Visar en lista över alla aktiva aviseringar om kommunikation mellan Operations Manager och Tjänstkartan.  Observera att dessa varningar inte är Operations Management Suite aviseringar som synkroniseras till Operations Manager. 

* **Servrar**: Visar de övervakade servrarna som är konfigurerade för synkronisering från Tjänstkartan.

    ![Övervaka servrar för Operations Manager-fönstret](media/oms-service-map/scom-monitoring-servers.png)

* **Datorn beroende Gruppvyer**: Visar en lista över alla datorgrupper som synkroniseras från Tjänstkartan. Du kan klicka på valfri grupp om du vill visa dess distribuerade program-diagram.

    ![Diagram för Operations Manager-distribuerade program](media/oms-service-map/scom-group-dad.png)

* **Servern beroende vyer**: Visar en lista över alla servrar som synkroniseras från Tjänstkartan. Du kan klicka på en server om du vill visa dess distribuerade program-diagram.

    ![Diagram för Operations Manager-distribuerade program](media/oms-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Redigera eller ta bort arbetsytan
Du kan redigera eller ta bort arbetsytan konfigurerade via den **översikt över tjänsten** fönstret (**Administration** fönstret > **Operations Management Suite**  >  **Service Map**). Du kan konfigurera en enda Operations Management Suite-arbetsyta för tillfället.

![Redigera arbetsytan för Operations Manager](media/oms-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurera regler och åsidosättningar
En regel _Microsoft.SystemCenter.ServiceMapImport.Rule_, skapas för att regelbundet hämta information från Tjänstkartan. Om du vill ändra tidsinställningar för synkronisering, kan du konfigurera åsidosättningar av regeln (**redigering** fönstret > **regler** > **Microsoft.SystemCenter.ServiceMapImport.Rule**) .

![Egenskapsfönstret för åsidosättningar i Operations Manager](media/oms-service-map/scom-overrides.png)

* **Aktiverad**: aktivera eller inaktivera automatiska uppdateringar. 
* **IntervalMinutes**: återställa tiden mellan uppdateringar. Standardintervallet är en timme. Du kan ändra värdet om du vill synkronisera server maps oftare.
* **TimeoutSeconds**: återställa hur lång tid innan tidsgränsen uppnås för begäran. 
* **TimeWindowMinutes**: återställa tidsfönstret för datafrågor. Standardvärdet är 60 minuter-fönstret. Det maximala värdet som tillåts av Tjänstkarta är 60 minuter.

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

Den aktuella designen innehåller följande problem och begränsningar:
* Du kan bara ansluta till en enda Operations Management Suite-arbetsyta.
* Du kan lägga till servrar i kartan servrar tjänstgruppen manuellt via den **redigering** rutan kartor för servrarna synkroniseras inte omedelbart.  De kommer att synkroniseras från Tjänstkarta under nästa synkronisering cykel.
* Om du gör några ändringar i diagrammet för distribuerade program som skapats av management pack ändringarna sannolikt att skrivas över på nästa synkronisering med Tjänstkartan.

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten
Officiell Azure-dokumentation om hur du skapar en tjänst huvudnamn finns:
* [Skapa ett huvudnamn för tjänsten med hjälp av PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Skapa ett huvudnamn för tjänsten med hjälp av Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Skapa ett huvudnamn för tjänsten med hjälp av Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Feedback
Du har feedback för oss om Tjänstkarta eller den här dokumentationen? Besök vår [User Voice sidan](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), där du kan föreslår funktioner eller rösta på förslag befintliga.
