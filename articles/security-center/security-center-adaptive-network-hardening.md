---
title: Anpassningsbar nätverk härdning i Azure Security Center | Microsoft Docs
description: " Lär dig hur du aktiverar anpassningsbar Härdning av nätverk i Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: monhaber
ms.openlocfilehash: cede54f69fbeec5e01c17d84436bdc4c9ee91002
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706655"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Anpassningsbar nätverk härdning i Azure Security Center
Lär dig hur du konfigurerar anpassningsbar Härdning av nätverk i Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Vad är anpassningsbara Härdning av nätverket?
Tillämpa [nätverkssäkerhetsgrupper (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) att filtrera trafik till och från resurser, förbättrar din säkerhetsposition i nätverket. Men kan det fortfarande finnas tillfällen där den faktiska trafik som passerar via NSG: N är en delmängd av NSG-reglerna som definierats. I dessa fall kan ytterligare förbättra säkerhetspositionen uppnås genom Härdning av NSG-regler baserat på de faktiska trafikmönster.

Anpassningsbar Härdning av nätverket innehåller rekommendationer för att ytterligare skydda NSG-reglerna. Den använder en machine learning-algoritm som omfattar faktiska trafik, kända betrodda konfiguration, hotintelligens och andra indikatorer för kompromettering, och ger rekommendationer för att tillåta trafik enbart från specifika IP-adress/port tupplar.

Anta exempelvis att den befintliga NSG-regeln är att tillåta trafik från 140.20.30.10/24 på port 22. Den anpassningsbara nätverk härdning rekommendation, baserat på analysen, skulle vara att begränsa intervallet och tillåta trafik från 140.23.30.10/29 – vilket är ett smalare IP-adressintervall, och neka all annan trafik till den porten.

![nätverket härdning vy](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


> [!NOTE]
> Anpassningsbar rekommendationer för härdning av nätverk stöds på följande portar: 22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Visa aviseringar för anpassningsbar Härdning av nätverk och regler

1. I Security Center väljer **nätverk** -> **anpassningsbar Härdning av nätverket**. De virtuella datorerna som nätverk visas under tre separata flikar:
   * **Skadade resurser**: Virtuella datorer som för närvarande har rekommendationer och aviseringar som utlösts genom att köra algoritmen anpassningsbar Härdning av nätverket. 
   * **Felfria resurser**: Virtuella datorer utan aviseringar och rekommendationer.
   * **Ej genomsökta resurser**: Virtuella datorer som algoritmen anpassningsbar Härdning av nätverket inte kan köras på grund av något av följande orsaker:
      * **Virtuella datorer är klassiska virtuella datorer**:-endast Azure Resource Manager virtuella datorer stöds.
      * **Det finns inte tillräckligt med data är tillgängliga**: Security Center kräver minst 30 dagar trafikdata för att generera korrekta trafik Härdning av rekommendationer.
      * **Den virtuella datorn inte skyddad av ASC-standard**: Endast virtuella datorer som är inställda på att Security Center Standard-prisnivån är berättigade till den här funktionen.

     ![skadade resurser](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Från den **skadade resurser** väljer du en virtuell dator för att visa dess aviseringar och rekommenderade härdningsreglerna tillämpas.

    ![Härdning aviseringar](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Granska och tillämpa anpassningsbar nätverk härdning rekommenderas regler

1. Från den **skadade resurser** väljer du en virtuell dator. Aviseringar och rekommenderade härdningsreglerna visas.
   ![Härdning aviseringar](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > Den **regler** fliken innehåller de regler som anpassningsbar Härdning av nätverket rekommenderar att du lägger till. Den **aviseringar** fliken visar en lista över de aviseringar som genererats på grund av trafiken som flödar till resursen, vilket inte är inom det IP-adressintervall som tillåts i de rekommenderade reglerna.

   ![härdningsreglerna](./media/security-center-adaptive-network-hardening/hardening-rules.png)

2. Om du vill ändra några av parametrarna för en regel kan du ändra den, enligt beskrivningen i [ändra en regel](#modify-rule).
   > [!NOTE]
   > Du kan också [ta bort](#delete-rule) eller [lägga till](#add-rule) en regel.

3. Välj vilka regler som du vill tillämpa på NSG: N och klickar på **tvinga**. 

### Ändra en regel  <a name ="modify-rule"> </a>

Du kanske vill ändra parametrarna för en regel som har rekommenderats. Du kan till exempel vill ändra de rekommendera IP-adressintervall.

Vissa viktiga riktlinjer för att ändra en anpassningsbar Härdning av Network-regel:

* Du kan ändra parametrarna för ”Tillåt” endast för regler. 
* Du kan inte ändra ”Tillåt” regler för att bli ”neka” regler. 

  > [!NOTE]
  > Skapa och ändra ”neka” regler görs direkt på en NSG för mer information, se [skapa, ändra eller ta bort en nätverkssäkerhetsgrupp](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* En **neka all trafik** regeln är den enda typen av regel för ”neka” som anges här och det kan inte ändras. Du kan dock ta bort den (se [ta bort en regel](#delete-rule)).
  > [!NOTE]
  > En **neka all trafik** regeln rekommenderas när, som ett resultat för att köra algoritmen Security Center identifierar inte trafik som ska tillåtas, baserat på den befintliga NSG-konfigurationen. Därför är den rekommenderade regeln som nekar all trafik till den angivna porten. Namnet på den här typen av regeln visas som ”systemgenererad”. Efter att framtvinga den här regeln, är dess faktiska namn i NSG: N en sträng som består av protokollet, trafikriktningen, ”DENY” och ett slumptal.

*Ändra en anpassningsbar Härdning av Network-regel:*

1. Att ändra några av parametrarna för en regel i den **regler** , klicka på de tre punkterna (...) i slutet av raden för regelns och på **Redigera regeln**.

   ![Redigera regel](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. I den **Redigera regeln** och uppdatera den information som du vill ändra och klicka på **spara**.

   > [!NOTE]
   > När du klickar på **spara**, du har ändrat regeln. *Men har du inte installerat den i NSG.* Om du vill använda det, måste du väljer du regeln i listan och klicka på **tvinga** (enligt beskrivningen i nästa steg).

3. Om du vill tillämpa den uppdaterade regeln från listan, väljer du uppdaterade regeln och klickar på **tvinga**.

### Lägg till en ny regel <a name ="add-rule"> </a>


Du kan lägga till en regel för ”Tillåt” som inte rekommenderas av Security Center.

> [!NOTE]
> Endast ”Tillåt” regler kan läggas till här. Om du vill lägga till ”neka” regler kan du göra det direkt på NSG: N. Mer information finns i [skapa, ändra eller ta bort en nätverkssäkerhetsgrupp](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Lägga till en regel för anpassningsbar Härdning av nätverk:*

1. Klicka på **Lägg till regel** (finns i det övre vänstra hörnet).

   ![Lägg till regel](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. I den **Redigera regeln** fönstret anger du informationen och klicka på **spara**.

   > [!NOTE]
   > När du klickar på **spara**, du har lagt till regeln och det visas med de rekommendera reglerna. Men har du inte installerat den på NSG: N. Om du vill aktivera den, måste du väljer du regeln i listan och klicka på **tvinga** (enligt beskrivningen i nästa steg).

3. Om du vill tillämpa den nya regeln från listan, Välj den nya regeln och klicka på **tvinga**.



### Ta bort en regel <a name ="delete-rule"> </a>

Om det behövs kan du ta bort en regel för rekommenderade. Du kan till exempel bestämma att tillämpa en föreslagen regel kan blockera legitima trafik.

*Ta bort en regel för anpassningsbar Härdning av nätverk:*

1. I den **regler** , klicka på de tre punkterna (...) i slutet av raden för regelns och på **ta Borttagningsregeln**.

   ![Ta bort regel](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

