---
title: Konfigurera agentlös beroendeanalys i Azure Migrate Server Assessment
description: Ställ in agentlös beroendeanalys i Azure Migrate Server Assessment.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: af767bf73a3b9a6f2a91298987f11974499fd694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455714"
---
# <a name="set-up-agentless-dependency-visualization"></a>Konfigurera agentlös beroendevisualisering 

I den här artikeln beskrivs hur du ställer in agentlös beroendeanalys i Azure Migrate:Server Assessment. [Beroendeanalys](concepts-dependency-visualization.md) hjälper dig att identifiera och förstå beroenden mellan datorer som du vill bedöma och migrera till Azure.


> [!IMPORTANT]
> Agentless beroende visualisering är för närvarande i förhandsversion för virtuella datorer med VMware, som upptäcktes med verktyget Azure Migrate:Server Assessment.
> Funktionerna kan vara begränsade eller ofullständiga.
> Den här förhandsversionen omfattas av kundsupport och kan användas för produktionsarbetsbelastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



## <a name="before-you-start"></a>Innan du börjar

- [Läs mer om](concepts-dependency-visualization.md#agentless-analysis) agentlös beroendeanalys.
- [Granska](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) förutsättningarna och supportkraven för att ställa in agentlös beroendevisualisering för virtuella datorer med VMware
- Kontrollera att du har [skapat](how-to-add-tool-first-time.md) ett Azure Migrate-projekt.
- Om du redan har skapat ett projekt kontrollerar du att du har [lagt till](how-to-assess.md) verktyget Azure Migrate:Server Assessment.
- Kontrollera att du har konfigurerat en [Azure Migrate-installation för](migrate-appliance.md) att identifiera dina lokala datorer. Läs om hur du konfigurerar en apparat för virtuella datorer [med VMware.](how-to-set-up-appliance-vmware.md) Installationen identifierar lokala datorer och skickar metadata och prestandadata till Azure Migrate:Server Assessment.


## <a name="current-limitations"></a>Aktuella begränsningar

- Just nu kan du inte lägga till eller ta bort en server från en grupp i beroendeanalysvyn.
- En beroendemappning för en grupp servrar är för närvarande inte tillgänglig.
- För närvarande kan beroendedata inte hämtas i tabellformat.

## <a name="create-a-user-account-for-discovery"></a>Skapa ett användarkonto för identifiering

Konfigurera ett användarkonto så att Serverutvärdering kan komma åt den virtuella datorn för identifiering. [Läs mer](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) om kontokrav.


## <a name="add-the-user-account-to-the-appliance"></a>Lägga till användarkontot i apparaten

Lägg till användarkontot i apparaten.

1. Öppna appen för hantering av apparaten. 
2. Navigera till informationspanelen **För att tillhandahålla vCenter.**
3. Klicka på **Lägg till autentiseringsuppgifter** i **Upptäck program och beroenden på virtuella datorer**
3. Välj **operativsystem,** ange ett eget namn för kontot och/**användarnamnet** **User name**
6. Klicka på **Spara**.
7. Klicka på **Spara och starta identifiering**.

    ![Lägg till vm-användarkonto](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Starta identifiering av beroende

Välj de datorer som du vill aktivera beroendeidentifiering på.

1. Klicka på **Identifierade servrar**i **Azure Migrate: Server Assessment**.
2. Klicka på ikonen **Beroendeanalys.**
3. Klicka på **Lägg till servrar**.
3. På sidan **Lägg till servrar** väljer du den installation som identifierar de relevanta datorerna.
4. Välj maskinerna i maskinlistan.
5. Klicka på **Lägg till servrar**.

    ![Starta identifiering av beroende](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Du kan visualisera beroenden runt sex timmar efter att du har startat beroendeidentifiering.

## <a name="visualize-dependencies"></a>Visualisera beroenden

1. Klicka på **Identifierade servrar**i **Azure Migrate: Server Assessment**.
2. Sök efter den dator du vill visa.
3. Klicka på **Visa beroenden** i kolumnen **Beroenden**
4. Ändra den tidsperiod för vilken du vill visa kartan med hjälp av listrutan **Tidslängd.**
5. Expandera **klientgruppen** för att lista datorerna med ett beroende på den valda datorn.
6. Expandera gruppen **Port** för att visa de datorer som har ett beroende från den valda datorn.
7. Om du vill navigera till kartvyn för någon av de beroende datorerna klickar du på maskinnamnet > **Läs in serverkartan**

    ![Expandera serverportgrupp och läs in serverkarta](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Expandera klientgrupp ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Expandera den valda datorn för att visa information på processnivå för varje beroende.

    ![Expandera server för att visa processer](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Processinformation för ett beroende är inte alltid tillgänglig. Om den inte är tillgänglig visas beroendet med processen markerad som Okänd process.

## <a name="stop-dependency-discovery"></a>Stoppa identifiering av beroende

Välj de datorer som du vill stoppa beroendeidentifiering på.

1. Klicka på **Identifierade servrar**i **Azure Migrate: Server Assessment**.
2. Klicka på ikonen **Beroendeanalys.**
3. Klicka på **Ta bort servrar**.
3. På sidan **Ta bort servrar** väljer du den **installation** som identifierar de virtuella datorer som du vill stoppa beroendeidentifieringen på.
4. Välj maskinerna i maskinlistan.
5. Klicka på **Ta bort servrar**.


## <a name="next-steps"></a>Nästa steg

[Gruppera maskinerna](how-to-create-a-group.md) för bedömning.
