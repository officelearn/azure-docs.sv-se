---
title: Övervakning och avisering av Azure Key Vault | Microsoft-dokument
description: Skapa en instrumentpanel för att övervaka hälsotillståndet för nyckelvalvet och konfigurera aviseringar.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: cc0d969ff6eb76732768dfed2826762920ae9e62
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726948"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Övervakning och avisering för Azure Key Vault

## <a name="overview"></a>Översikt

När du har börjat använda nyckelvalv för att lagra dina produktionshemligheter är det viktigt att övervaka hälsan hos ditt nyckelvalv för att se till att tjänsten fungerar som avsett. När du börjar skala din tjänst kommer antalet förfrågningar som skickas till ditt nyckelvalv att öka. Detta kan öka svarstiden för dina begäranden och i extrema fall, orsaka dina begäranden som begränsas vilket kommer att påverka prestanda för din tjänst. Du måste också bli aviserad om nyckelvalvet skickar ovanligt många felkoder, så att du snabbt kan meddelas om eventuella problem med åtkomstprinciper eller brandväggskonfiguration. Det här dokumentet innehåller följande avsnitt:

+ Grundläggande key vault-mått för att övervaka
+ Konfigurera mått och skapa en instrumentpanel 
+ Så här skapar du aviseringar vid angivna tröskelvärden 

## <a name="basic-key-vault-metrics-to-monitor"></a>Grundläggande key vault-mått för att övervaka

+ Arkiv Tillgänglighet  
+ Valv Mättnad 
+ Svarstid för tjänstens API 
+ Totalt antal service-API-träffar (filter efter aktivitetstyp) 
+ Felkoder (Filtrera efter statuskod) 

**Arkiv tillgänglighet** - Det här måttet bör alltid vara på 100% detta är ett viktigt mått att övervaka, eftersom det snabbt kan visa dig om din nyckel valv upplevt ett avbrott. 

**Valv mättnad** – Antalet begäranden per sekund som ett nyckelvalv kan tjäna baseras på vilken typ av åtgärd som utförs. Vissa valvåtgärder har ett lägre tröskelvärde för begäranden per sekund. Det här måttet sammanställer den totala användningen av nyckelvalvet för alla åtgärdstyper för att komma med ett procentvärde som anger din aktuella nyckelvalvsanvändning. En fullständig lista över servicegränser för nyckelvalv finns i följande dokument. [Begränsningar för tjänsten Azure Key Vault](service-limits.md)

**Svarstid för tjänst-API** - Det här måttet visar den genomsnittliga svarstiden för ett anrop till nyckelvalv. Även om nyckelvalvet kan ligga inom tjänstgränserna kan ett högt utnyttjande av nyckelvalvet införa svarstid som gör att program nedströms misslyckas. 

**Totalt antal API-träffar** - Det här måttet visar alla anrop som görs till ditt nyckelvalv. Detta hjälper dig att identifiera vilka program som anropar ditt nyckelvalv. 

**Felkoder** – Det här måttet visar dig om ditt nyckelvalv har en ovanlig mängd fel. En fullständig lista över felkoder och felsökningsvägledning finns i följande dokument. [AZURE Key Vault REST API-felkoder](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Konfigurera mått och skapa en instrumentpanel

1. Logga in på Azure-portalen
2. Navigera till key vault
3. Välj **mått** under **Övervakning** 

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-1.png)

4. Uppdatera diagrammets rubrik till vad du vill se på instrumentpanelen. 
5. Välj scope. I det här exemplet väljer vi ett enda nyckelvalv. 
6. Välj **måttöver hela valvtillgänglighet** och aggregeringsavg **Avg** 
7. Uppdatera tidsintervallet till de senaste 24 timmarna och uppdatera tidsgranulariteten till 1 minut. 

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-2.png)

8. Upprepa stegen ovan för måtten för svarstider för arkivsatturer och service-API. Välj **Fäst på instrumentpanelen** om du vill spara mätvärdena i en instrumentpanel. 

> [!IMPORTANT]
> Välj "Fäst på instrumentpanelen" och spara alla mått som du konfigurerar. Om du lämnar sidan och återgår till den utan att spara kommer dina konfigurationsändringar att gå förlorade. 

9. Om du vill övervaka alla typer av åtgärder i nyckelvalvet använder du måttet **Totalt service-API träffar** och väljer **Använd delning efter aktivitetstyp**

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-3.png)

10. Om du vill övervaka felkoder i nyckelvalvet använder du måttet **Totalt service-API-resultat** och väljer **Använd delning efter aktivitetstyp**

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-4.png)

Nu har du en instrumentpanel som ser ut så här. Du kan klicka på de tre punkterna längst upp till höger på varje bricka och du kan ordna om och ändra storlek på brickorna som du behöver. 

När du har sparat och publicerat instrumentpanelen skapas en ny resurs i din Azure-prenumeration. Du kommer att kunna se det när som helst genom att söka efter "delad instrumentpanel". 

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Konfigurera aviseringar i key vault 

Det här avsnittet visar hur du konfigurerar aviseringar i nyckelvalvet så att du kan varna ditt team att vidta åtgärder omedelbart om nyckelvalvet är i feltillstånd. Du kan konfigurera aviseringar som skickar ett e-postmeddelande, helst till ett team DL, avfyra ett händelserutnätsmeddelande eller ringa eller skicka ett telefonnummer till ett telefonnummer. Du kan också välja statiska aviseringar baserat på ett fast värde, eller en dynamisk avisering som varnar dig om ett övervakat mått överskrider den genomsnittliga gränsen för ditt nyckelvalv ett visst antal gånger inom ett definierat tidsintervall. 

> [!IMPORTANT]
> Observera att det kan ta upp till 10 minuter för nykonfigurerade aviseringar att börja skicka meddelanden. 

### <a name="configure-an-action-group"></a>Konfigurera en åtgärdsgrupp 

En åtgärdsgrupp är en konfigurerbar lista över meddelanden och egenskaper.

1. Logga in på Azure-portalen
2. Sök efter **aviseringar** i sökrutan
3. Välj **Hantera åtgärder**

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-6.png)

4. Välj **+ Lägg till åtgärdsgrupp**

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-7.png)

5. Välj **åtgärdstyp** för din åtgärdsgrupp. I det här exemplet skapar vi en e-postavisering.

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-9.png)

6. Klicka på **OK** längst ned på sidan. Du har skapat en åtgärdsgrupp. 

Nu när du har konfigurerat en åtgärdsgrupp konfigurerar vi tröskelvärdena för nyckelvalvsavisering. 

### <a name="configure-alert-thresholds"></a>Konfigurera tröskelvärden för aviseringar 

1. Välj din nyckelvalvresurs i Azure-portalen och välj **Aviseringar** under **Övervakning**

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-10.png)

2. Välj **ny varningsregel**

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-11.png)

3. Välj omfattningen av varningsregeln. Du kan välja ett enda valv eller flera. 

> [!IMPORTANT]
> Observera att när du väljer flera valv för omfattningen av dina aviseringar måste alla valda valv finnas i samma region. Du måste konfigurera separata varningsregler för valv i olika regioner. 

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-12.png)

4. Välj villkoren för dina aviseringar. Du kan välja någon av följande signaler och definiera din logik för aviseringar. Key Vault-teamet rekommenderar att du konfigurerar följande tröskelvärden för aviseringar. 

    + Tillgängligheten för nyckelvalv sjunker under 100 % (statiskt tröskelvärde)
    + Svarstiden för nyckelvalv är större än 500 ms (statiskt tröskelvärde) 
    + Total valvmättnad är större än 75 % (statiskt tröskelvärde) 
    + Total valvsmättnad överstiger medelvärdet (dynamiskt tröskelvärde)
    + Totalt antal felkoder som är högre än genomsnittet (dynamiskt tröskelvärde) 

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Exempel 1: Konfigurera ett statiskt tröskelvärde för svarstid för svarstid

Välj **Total service-API-svarstid** som signalnamn
> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-14.png)

Se följande konfigurationsparametrar.

+ Ange tröskelvärdet till **statisk** 
+ Ställ in operatören **på Större än**
+ Ange aggregeringstypen till **Medelvärde**
+ Ange tröskelvärdet till **500**
+ Ange aggregeringsperiod till **5 minuter**
+ Ställ in utvärderingsfrekvensen på **1 minut**
+ Välj **Klar**  

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Exempel 2: Konfigurera ett dynamiskt tröskelvärde för valvmättnad 

När du använder en dynamisk avisering kan du se historiska data för nyckelvalvet som du har valt. Det blå området representerar den genomsnittliga användningen av ditt nyckelvalv. Det röda området visar toppar som skulle ha utlöst en avisering förutsatt att andra villkor i aviseringskonfigurationen är uppfyllda. De röda punkterna visar förekomster av överträdelser där kriterierna för aviseringen uppfylldes under det aggregerade tidsfönstret. Du kan ställa in en avisering för brand efter ett visst antal överträdelser inom en viss tid. Om du inte vill inkludera tidigare data finns det ett alternativ för att utesluta gamla data nedan i avancerade inställningar. 

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-16.png)

Se följande konfigurationsparametrar.

+ Ange tröskelvärdet till **dynamisk** 
+ Ställ in operatören **på Större än**
+ Ange aggregeringstypen till **Medelvärde**
+ Ange tröskelkänsligheten till **medium**
+ Ange aggregeringsperiod till **5 minuter**
+ Ställ in utvärderingsfrekvensen på **1 minut**
+ **Valfritt** Konfigurera avancerade inställningar 
+ Välj **Klar**

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-17.png)

5. Lägga till den åtgärdsgrupp som du har konfigurerat

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-18.png)

6. Aktivera aviseringen och tilldela en allvarlighetsgrad

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-19.png)

7. Skapa aviseringen 


## <a name="next-steps"></a>Nästa steg

Grattis, du har nu skapat en övervakningsinstrumentpanel och konfigurerat aviseringar för ditt nyckelvalv! När du har följt alla steg ovan bör du få e-postaviseringar när nyckelvalvet uppfyller de varningsvillkor som du har konfigurerat. Ett exempel på detta visas nedan. Använd de verktyg som du har ställt in i den här artikeln för att aktivt övervaka hälsotillståndet för nyckelvalvet. 

### <a name="example-email-alert"></a>Exempel på e-postavisering 

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portalen](../media/alert-20.png)
