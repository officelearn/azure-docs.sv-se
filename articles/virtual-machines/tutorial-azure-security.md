---
title: Självstudie – använda Azure Security Center för virtuella datorer i Azure
description: I den här självstudien får du lära dig mer om Azure Security Center funktioner som hjälper dig att skydda och skydda dina virtuella datorer i Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/11/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: aec223ceca242e37e25f9e7288c8e0c0e2ea7535
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595249"
---
# <a name="tutorial-use-azure-security-center-to-monitor-virtual-machines"></a>Självstudie: Använd Azure Security Center för att övervaka virtuella datorer

Med Azure Security Center kan du få bättre överblick över säkerhetsåtgärderna för Azure-resurserna. Security Center tillhandahåller integrerad säkerhetsövervakning. Här kan du upptäcka hot som annars kan förbli oupptäckta. I den här självstudiekursen får du lära dig mer om Azure Security Center och hur du gör följande:
 
> [!div class="checklist"]
> * Konfigurera datainsamling
> * Konfigurera säkerhetsprinciper
> * Visa och åtgärda problem med konfigurationshälsan
> * Granska identifierade hot

## <a name="security-center-overview"></a>Översikt över Security Center

Security Center identifierar potentiella konfigurationsproblem för virtuella datorer och potentiella mål för säkerhetsintrång. Dessa problem kan omfatta virtuella datorer som saknar nätverkssäkerhetsgrupper, okrypterade diskar och brute force-attacker med Remote Desktop Protocol (RDP). Informationen visas på instrumentpanelen i Security Center i form av lättförståeliga diagram.

För att komma åt instrumentpanelen i Security Center går du till Azure-portalen och väljer **Security Center** i menyn. På instrumentpanelen visas säkerhetshälsa för Azure-miljön, antalet aktuella rekommendationer och aktuell status för hotaviseringar. Du kan expandera varje diagram på den högsta nivån för att visa mer information.

![Instrumentpanelen i Security Center](./media/tutorial-azure-security/asc-dash.png)

Security Center gör mer än att identifiera data och lämna rekommendationer för de problem som upptäcks. Om en virtuell dator har distribuerats utan en ansluten nätverkssäkerhetsgrupp visar Security Center en rekommendation med åtgärder som du kan vidta. Du kan avhjälpa problemen automatiskt utan att behöva stänga Security Center.  

![Skärm bild som visar sidan rekommendationer, som innehåller en beskrivning, resurs, tillstånd och allvarlighets grad för rekommendationer.](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Konfigurera datainsamling

Innan du kan få en överblick över säkerhetskonfigurationerna för virtuella datorer måste du ställa in datainsamling i Security Center. I det här ingår att aktivera datainsamling. Då installeras automatiskt Microsoft Monitoring Agent på alla virtuella datorer i din prenumeration.

1. Gå till instrumentpanelen i Security Center, klicka på **Säkerhetsprincip** och markera sedan din prenumeration. 
2. För **Datainsamling** i **Automatisk etablering** väljer du **På**.
3. För **Standardkonfiguration för arbetsyta** lämnar du värdet **Använd arbetsyta/arbetsytor som skapats av Security Center (standard)**.
4. Under **Säkerhetshändelser** behåller du standardalternativet **Vanliga**.
4. Klicka på **Spara** längst upp på sidan. 

Datainsamlingsagenten från Security Center installeras på alla virtuella datorer och datainsamlingen påbörjas. 

## <a name="set-up-a-security-policy"></a>Konfigurera en säkerhetsprincip

Säkerhetsprinciper används för att definiera objekten för vilka Security Center samlar in data och lämnar rekommendationer. Du kan använda olika säkerhetsprinciper för olika uppsättningar av Azure-resurser. Även om Azures standardresurser ska utvärderas mot alla principobjekt kan du inaktivera enskilda principobjekt för alla Azure-resurser eller för en viss resursgrupp. Detaljerad information om säkerhetsprinciper i Security Center finns i [Ange säkerhetsprinciper i Azure Security Center](../security-center/tutorial-security-policy.md). 

Så här ställer du in en säkerhetsprincip för en hel prenumeration:

1. Gå till instrumentpanelen i Security Center, välj **Säkerhetsprincip** och välj sedan din prenumeration.
2. På bladet **Säkerhetsprincip** väljer du **Säkerhetsprincip**. 
3. På bladet **Säkerhetsprincip – Säkerhetsprincip** aktiverar eller inaktiverar du de principobjekt du vill använda för prenumerationen.
4. När du är färdig väljer du **Spara** längst upp på bladet. 

![Unik princip](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Visa konfigurationshälsa för virtuella datorer

När du har aktiverat datainsamling och angett en säkerhetsprincip börjar Security Center att skicka aviseringar och rekommendationer. Datainsamlingsagenterna installeras i takt med att de virtuella datorerna distribueras. Security Center fylls sedan med data från de nya virtuella datorerna. Detaljerad information om konfigurationshälsa för virtuella datorer finns i [Protect your VMs in Security Center](../security-center/asset-inventory.md) (Skydda dina virtuella datorer i Security Center). 

När data samlats in sammanställs information om resurshälsa för varje virtuell dator och relaterad Azure-resurs. Informationen visas i ett lättförståeligt diagram. 

Så här visar du information om resurshälsa:

1.  Under **Skydd** på instrumentpanelen i Security Center väljer du **Beräkning**. 
2.  På bladet **Beräkning** väljer du **Virtuella datorer och vanliga datorer**. Den här vyn visar en sammanfattning av konfigurationsstatusarna för alla dina virtuella datorer.

![Beräkningshälsa](./media/tutorial-azure-security/compute-health.png)

Markera en virtuell dator för att visa alla rekommendationer för den virtuella datorn. 

## <a name="remediate-configuration-issues"></a>Åtgärda konfigurationsproblem

När Security Center börjar fyllas med konfigurationsdata görs rekommendationer utifrån den säkerhetsprincip som du har konfigurerat. Om du t.ex. har konfigurerat en virtuell dator utan en associerad nätverkssäkerhetsgrupp får du en rekommendation om att skapa en. 

Gör så här för att visa en lista över alla rekommendationer: 

1. Gå till instrumentpanelen i Security Center och välj **Rekommendationer**.
2. Välj en specifik rekommendation. Då visas en lista över alla resurser som rekommendationen gäller för.
3. Välj en resurs om du vill tillämpa en rekommendation. 
4. Följ reparationsstegen. 

I många fall kan Security Center tillhandahålla lämpliga åtgärder som du kan vidta för att följa en rekommendation utan att behöva lämna Security Center. I följande exempel identifierar Security Center en nätverkssäkerhetsgrupp som har en regel för obegränsat inkommande. På rekommendationssidan kan du klicka på knappen **Redigera regler för inkommande trafik**. Användargränssnittet som behövs för att ändra regeln visas. 

![Rekommendationer](./media/tutorial-azure-security/remediation.png)

I takt med att rekommendationerna åtgärdats markeras de som lösta. 

## <a name="view-detected-threats"></a>Visa identifierade hot

Utöver konfigurationsrekommendationer för resurserna visas även aviseringar om identifierade säkerhetshot. Funktionen för säkerhetsvarningar sammanställer de data som samlas in från varje virtuell dator, Azures nätverksloggar och anslutna partnerlösningar i syfte att upptäcka säkerhetshot mot Azure-resurserna. Mer detaljerad information om Security Center hot identifierings funktioner finns i [Security Center identifiera hot?](../security-center/security-center-alerts-overview.md#detect-threats).

Om du vill kunna använda funktionen för säkerhetsvarningar måste du uppgradera prisnivån för Security Center från *Kostnadsfri* till *Standard*. Om du uppgraderar till en högre prisnivå kan du använda dig av en **kostnadsfri utvärderingsversion**. 

Så här ändrar du prisnivån:  

1. Gå till instrumentpanelen i Security Center, klicka på **Säkerhetsprincip** och markera sedan din prenumeration.
2. Välj **pris nivå**.
3. Välj **Standard** och klicka sedan på **Spara** längst upp på bladet.


När du har ändrat prisnivån fylls diagrammet för säkerhetsvarningarna på i takt med att säkerhetshoten upptäcks.

![Säkerhetsaviseringar](./media/tutorial-azure-security/security-alerts.png)

Välj en avisering för att visa information. Du kan t.ex. visa en beskrivning av hotet, identifieringstid, alla intrångsförsök och rekommenderade åtgärder. I följande exempel upptäcktes en RDP-baserad brute force-attack med 294 misslyckade RDP-försök. En rekommenderad lösning på problemet tillhandahålls.

![RDP-attack](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen har du konfigurerat Azure Security Center och granskat virtuella datorer i Security Center. Du har lärt dig att:

> [!div class="checklist"]
> * Konfigurera datainsamling
> * Konfigurera säkerhetsprinciper
> * Visa och åtgärda problem med konfigurationshälsan
> * Granska identifierade hot

Gå vidare till nästa självstudiekurs om vill veta mer om hur du skapar en CI/CD-pipeline med Jenkins, GitHub och Docker.

> [!div class="nextstepaction"]
> [Skapa CI/CD-infrastruktur med Jenkins, GitHub och Docker](/azure/developer/jenkins/pipeline-with-github-and-docker)