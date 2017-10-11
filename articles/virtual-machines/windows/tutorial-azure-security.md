---
title: Azure Security Center och Windows-datorer i Azure | Microsoft Docs
description: "Läs mer om säkerheten för din Windows Azure-dator med Azure Security Center."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: adb00e28b0b204858a763f83836ee2ac96f8f9e4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="monitor-virtual-machine-security-by-using-azure-security-center"></a>Övervaka virtuella säkerhet med hjälp av Azure Security Center

Azure Security Center kan hjälpa dig få insyn i Azure-resurs säkerhetsåtgärder. Security Center ger övervakning av integrerad säkerhet. Det kan upptäcka hot som annars kan förbli oupptäckta. I kursen får du lära dig om Azure Security Center och hur du:
 
> [!div class="checklist"]
> * Konfigurera datainsamling
> * Ställa in säkerhetsprinciper
> * Visa och lösa konfigurationsproblem hälsa
> * Granska identifierade hot  

## <a name="security-center-overview"></a>Översikt över Security Center

Security Center identifierar potentiella konfigurationsproblem för virtuell dator (VM) och mål säkerhetshot. Dessa kan innehålla virtuella datorer som saknar nätverkssäkerhetsgrupper och okrypterade diskar brute force-attacker för Remote Desktop Protocol (RDP). Informationen visas på instrumentpanelen i Security Center i lätt att läsa diagram.

För att komma åt instrumentpanelen i Security Center i Azure-portalen på menyn, Välj **Security Center**. På instrumentpanelen, se säkerhetshälsa för Azure-miljön, hitta antalet aktuella rekommendationer och visa aktuell status för hot aviseringar. Du kan expandera varje övergripande diagram om du vill se mer information.

![Instrumentpanelen Security Center](./media/tutorial-azure-security/asc-dash.png)

Security Center är mer omfattande än identifiering av data för att ge rekommendationer för problem som upptäcks. Om en virtuell dator har distribuerats utan ett anslutet nätverkssäkerhetsgrupp, visar Säkerhetscenter en rekommendation med steg du kan vidta. Du kan hämta automatiska reparationer utan att lämna samband med Security Center.  

![Rekommendationer](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Konfigurera datainsamling

Innan du kan få en överblick över VM säkerhetskonfigurationer, måste du ställer in insamling av Security Center. Detta innebär att aktivera insamling av data och skapa ett Azure storage-konto för att lagra data som samlats in. 

1. Klicka på instrumentpanelen i Security Center **säkerhetsprincip**, och sedan välja din prenumeration. 
2. För **datainsamling**väljer **på**.
3. Om du vill skapa ett lagringskonto **väljer något lagringskonto**. Markera **OK**.
4. På den **säkerhetsprincip** bladet väljer **spara**. 

Security Center data collection agent installeras på alla virtuella datorer och datainsamlingen påbörjas. 

## <a name="set-up-a-security-policy"></a>Ställ in en säkerhetsprincip

IPSec-principer används för att definiera objekten som Säkerhetscenter samlar in data och ger rekommendationer. Du kan använda olika principer för olika uppsättningar av Azure-resurser. Även om Azure-resurser som standard ska utvärderas mot alla principobjekt kan du inaktivera enskilda principobjekt för alla Azure-resurser eller för en resursgrupp. Detaljerad information om säkerhetsprinciper från security Center finns [ställa in säkerhetsprinciper i Azure Security Center](../../security-center/security-center-policies.md). 

Att ställa in en säkerhetsprincip för alla Azure-resurser:

1. Välj på instrumentpanelen i Security Center **säkerhetsprincip**, och sedan välja din prenumeration.
2. Välj **skyddsprincip**.
3. Aktivera eller inaktivera principobjekt som du vill koppla till alla Azure-resurser.
4. När du är klar med att välja inställningarna väljer **OK**.
5. På den **säkerhetsprincip** bladet väljer **spara**. 

Att konfigurera en princip för en viss resursgrupp:

1. Välj på instrumentpanelen i Security Center **säkerhetsprincip**, och välj sedan en resursgrupp.
2. Välj **skyddsprincip**.
3. Aktivera eller inaktivera principobjekt som du vill koppla till resursgruppen.
4. Under **arv**väljer **unik**.
5. När du är klar med att välja inställningarna väljer **OK**.
6. På den **säkerhetsprincip** bladet väljer **spara**.  

Du kan också stänga av insamling av data för en viss resursgrupp på den här sidan.

I följande exempel skapas en unik princip för en resursgrupp med namnet *myResoureGroup*. Disk kryptering och web application brandväggen rekommendationer är inaktiverade i den här principen.

![Unik princip](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Visa konfigurationshälsa för VM

När du har aktiverat datainsamling och ange en säkerhetsprincip för, börjar Security Center att ge aviseringar och rekommendationer. Eftersom virtuella datorer distribueras är data collection agenten installerad. Security Center består av data för de nya virtuella datorerna. Detaljerad information om hälsa för VM-konfiguration, se [skydda dina virtuella datorer i Security Center](../../security-center/security-center-virtual-machine-recommendations.md). 

När data har samlats in sammanställs resurshälsa för varje virtuell dator och relaterade Azure-resurs. Informationen visas i ett enkelt att läsa diagram. 

Visa resurshälsa:

1.  På Security Center instrumentpanelen under **resurssäkerhetshälsa**väljer **Compute**. 
2.  På den **Compute** bladet väljer **virtuella datorer**. Den här vyn visar en sammanfattning av status för konfiguration för dina virtuella datorer.

![Beräkna hälsa](./media/tutorial-azure-security/compute-health.png)

Om du vill se alla rekommendationer för en virtuell dator, väljer du den virtuella datorn. Rekommendationer och reparation beskrivs närmare i nästa avsnitt i den här kursen.

## <a name="remediate-configuration-issues"></a>Åtgärda problem med konfiguration

När Security Center börjar fylla med konfigurationsdata, görs rekommendationer baserat på den säkerhetsprincip som du konfigurerar. Till exempel om en virtuell dator har konfigurerats utan en nätverkssäkerhetsgrupp görs en rekommendation för att skapa en. 

Visa en lista över alla rekommendationer: 

1. Välj på instrumentpanelen i Security Center **rekommendationer**.
2. Välj en specifik rekommendation. En lista över alla resurser som rekommendationen gäller visas.
3. Välj en specifik resurs för att tillämpa en rekommendation. 
4. Följ anvisningarna för steg. 

I många fall innehåller Security Center tillämplig steg som du kan vidta en rekommendation utan att lämna Security Center. I följande exempel identifierar Security Center en nätverkssäkerhetsgrupp som har en obegränsad inkommande regel. På sidan rekommendation kan du välja den **redigera regler för inkommande trafik** knappen. Användargränssnittet som behövs för att ändra regeln visas. 

![Rekommendationer](./media/tutorial-azure-security/remediation.png)

De är märkta som löst som rekommendationer har åtgärdats. 

## <a name="view-detected-threats"></a>Visa identifierade hot

Förutom resurs konfigurationsrekommendationer visas hotidentifieringsaviseringar i Security Center. Aviseringar säkerhetsfunktion sammanställer data som samlas in från varje VM, Azure nätverk loggar och anslutna partnerlösningar att upptäcka säkerhetshot mot Azure-resurser. För detaljerad information om funktionerna i Security Center threat detection Se [identifieringsfunktionerna i Azure Security Center](../../security-center/security-center-detection-capabilities.md).

Aviseringar säkerhetsfunktion kräver Security Center prisnivån ökas från *lediga* till *Standard*. En 30-dagars **kostnadsfri utvärderingsversion** är tillgänglig när du flyttar till den här högre prisnivå. 

Ändra prisnivån:  

1. Klicka på instrumentpanelen i Security Center **säkerhetsprincip**, och sedan välja din prenumeration.
2. Välj **prisnivå**.
3. Välj ny nivå och välj sedan **Välj**.
4. På den **säkerhetsprincip** bladet väljer **spara**. 

När du har ändrat prisnivån börjar säkerhet aviseringar diagrammet fylla som säkerhet hot upptäcks.

![Säkerhetsaviseringar](./media/tutorial-azure-security/security-alerts.png)

Välj en avisering för att visa information. Du kan till exempel se en beskrivning av hotet, identifieringstid, alla hot försök och de rekommenderade åtgärderna. I följande exempel upptäcktes en RDP brute force-attacker med 294 RDP lösenordsförsök. En rekommenderad lösning på problemet.

![RDP-attack](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen, Ställ in Azure Security Center och granskas virtuella datorer i Security Center. Du har lärt dig hur till:

> [!div class="checklist"]
> * Konfigurera datainsamling
> * Ställa in säkerhetsprinciper
> * Visa och lösa konfigurationsproblem hälsa
> * Granska identifierade hot

Gå vidare till nästa kurs att lära dig hur du skapar en CI/CD-pipeline med Visual Studio Team Services och en Windows virtuell dator som kör IIS.

> [!div class="nextstepaction"]
> [Visual Studio Team Services CI/CD-pipeline](./tutorial-vsts-iis-cicd.md)
