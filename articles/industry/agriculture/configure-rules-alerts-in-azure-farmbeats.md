---
title: Konfigurera regler och hantera aviseringar
description: Beskriver hur du konfigurerar regler och hanterar aviseringar i FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482990"
---
# <a name="configure-rules-and-manage-alerts"></a>Konfigurera regler och hantera aviseringar

Med Azure FarmBeats kan du skapa regler baserat på affärslogiken, förutom sensordata som flödar från sensorer och enheter som distribueras i din servergrupp. Reglerna utlöser varningar i systemet när sensorvärden korsar ett tröskelvärde. Genom att visa och analysera aviseringar som skapats efter tröskelvärdena kan du snabbt agera på alla problem och få nödvändiga lösningar.

## <a name="create-rule"></a>Skapa regel

1. Gå till **Regler**på startsidan .
2. Välj **Ny regel**. Fönstret Ny regel visas.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Ange **regelnamn** och **regelbeskrivning** och välj sedan en servergrupp på listrutan **Välj servergrupp.**
4. Skriv servergruppens namn för att välja avsnittet **Servergrupp** och Villkor visas i samma fönster.  

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. I **Villkor**anger du värdena för **Measure**, **Operator** och **Value**.
6. Skriv måttnamnet på listrutan **Mät.**
7. Välj **+Lägg till villkor** om du vill lägga till fler villkor i regeln.
8. Välj **allvarlighetsgrad**.
9. I **Åtgärd**aktiverar du knappen **Skicka e-post** för att aktivera e-postaviseringar.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Ange de **e-postadresser** som du vill skicka e-postaviseringen till, tillsammans med **e-postämnet** och **ytterligare anteckningar**.  
11. Aktivera eller inaktivera regeln **Enabled** i **regelstatusen.**
    Du kan visa antalet enheter som påverkas av regeln.
12. Välj **Använd** om du vill skapa regeln.

## <a name="view-rule"></a>Visa regel

På **sidan Servering** visas en lista över tillgängliga regler. Välj ett **regelnamn**. I ett fönster visas följande information som gäller för den valda regeln:
 - Regelnamn
 - Länk till den servergrupp som regeln är associerad med
 - Skapat datum
 - Senast uppdaterat datum
 - Allvarlighetsgrad
 - Regelstatus
 - Lista över villkor  
 - Antal enheter som påverkas av regeln

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Redigera regel

Så här redigerar du en regel:

1. Välj **Regler** på startsidan på den vänstra navigeringsmenyn.
   Regelfönstret visas.
2. Välj den regel som du vill redigera för.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Välj **Redigera** i åtgärdsfältet visas fönstret **Redigera regel.**

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Ändra **regelnamn**och **regelbeskrivning** och välj sedan en servergrupp på listrutan **Välj servergrupp.**
5. Skriv servergruppens namn för att välja servergruppen och **Villkor** visas i samma fönster.  
6. I **Villkor**redigerar **du Mått**, **Operator** och **Värde**.
7. Skriv måttnamnet på listrutan **Mät.**
8. Välj **+Lägg till villkor** om du vill lägga till/redigera villkor i reglerna.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Välj **allvarlighetsgrad**.  
10. I **Åtgärd**aktiverar du knappen **Skicka e-post** för att aktivera e-postaviseringar.
11. Redigera de **e-postadresser** som du vill skicka e-postaviseringen tillsammans med **e-postämnet** och **ytterligare anteckningar**.  
12. Aktivera eller inaktivera regeln **Enabled** i **regelstatusen.**
Du kan visa antalet enheter som påverkas av den här regeln.
13. Välj **Använd** om du vill redigera regeln.

## <a name="change-rule-status"></a>Ändra regelstatus

Så här ändrar du status för en regel:

1. Välj **Regler** på startsidan på den vänstra navigeringsmenyn. Regelfönstret visas.
2. Välj den regel som du vill ändra status för.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Välj **Ändra status** i åtgärdsfältet. Fönstret **Ändra status** visas.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Ändra regelstatus med växlingsknappen **Ändra status.**
   Du kan visa antalet enheter som påverkas av regeln.
4. Välj **Använd** om du vill ändra regelstatus.

## <a name="delete-rule"></a>Ta bort regel

Så här tar du bort en regel:

1. Välj **Regler** på startsidan på den vänstra navigeringsmenyn. Regelfönstret visas.
2. Välj den regel som du vill ta bort.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Välj **Ta bort** i åtgärdsfältet.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. Dialogrutan **Ta bort regel** visas. Välj **Ta bort**.
