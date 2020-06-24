---
title: Konfigurera beroende analys utan agent i Azure Migrate Server bedömning
description: Konfigurera en agent lös beroende analys i Azure Migrate Server bedömning.
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: dc2ea0656198927cc8ae58533d296a2bedc37c13
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771384"
---
# <a name="analyze-machine-dependencies-agentless"></a>Analysera datorberoenden (agentlösa)

I den här artikeln beskrivs hur du konfigurerar en agent lös beroende analys i Azure Migrate: Server bedömning. Beroende [analys](concepts-dependency-visualization.md) hjälper dig att identifiera och förstå beroenden mellan datorer för utvärdering och migrering till Azure.


> [!IMPORTANT]
> Det finns för närvarande en för hands version av en beroende visualisering för virtuella VMware-datorer som har identifierats med verktyget Azure Migrate: Server bedömning.
> Funktioner kan vara begränsade eller ofullständiga.
> Den här för hands versionen täcks av kund support och kan användas för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Aktuella begränsningar

- I vyn beroende analys kan du för närvarande inte lägga till eller ta bort en server från en grupp.
- En beroende karta för en grupp med servrar är inte tillgänglig för närvarande.
- Det går inte att hämta beroende data i tabell format.

## <a name="before-you-start"></a>Innan du börjar

- [Granska](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) operativ system som stöds och de behörigheter som krävs.
- Kontrol lera att du:
    - Ha ett Azure Migrate-projekt. Om du inte gör det [skapar](how-to-add-tool-first-time.md) du en nu.
    - Kontrol lera att du har [lagt](how-to-assess.md) till verktyget Azure Migrate: Server utvärderings verktyg i projektet.
    - Konfigurera en [Azure Migrate-apparat](migrate-appliance.md) för att identifiera lokala datorer. [Konfigurera en installation](how-to-set-up-appliance-vmware.md) för virtuella VMware-datorer. Enheten identifierar lokala datorer och skickar metadata-och prestanda data till Azure Migrate: Server utvärdering.
- Kontrol lera att VMware-verktygen (senare än 10,2) är installerade på varje virtuell dator som du vill analysera.


## <a name="create-a-user-account-for-discovery"></a>Skapa ett användar konto för identifiering

Konfigurera ett användar konto så att Server utvärderingen kan komma åt den virtuella datorn för att identifiera beroenden. [Lär dig](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) mer om konto krav för virtuella Windows-och Linux-datorer.


## <a name="add-the-user-account-to-the-appliance"></a>Lägg till användar kontot till enheten

Lägg till användar kontot till enheten.

1. Öppna appen för hantering av appar. 
2. Navigera till panelen **Tillhandahåll vCenter-information** .
3. I **identifiera program och beroenden på virtuella datorer**klickar du på **Lägg till autentiseringsuppgifter**
3. Välj **operativ system**, ange ett eget namn för kontot och **User name** / **lösen ordet** för användar namn
6. Klicka på **Spara**.
7. Klicka på **Spara och starta identifiering**.

    ![Lägg till användar konto för virtuell dator](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Starta beroende identifiering

Välj de datorer där du vill aktivera beroende identifiering.

1. Klicka på **identifierade servrar**i **Azure Migrate: Server bedömning**.
2. Klicka på ikonen **beroende analys** .
3. Klicka på **Lägg till servrar**.
4. På sidan **Lägg till servrar** väljer du den installation som identifierar relevanta datorer.
5. Välj datorerna i listan dator.
6. Klicka på **Lägg till servrar**.

    ![Starta beroende identifiering](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Du kan visualisera beroenden runt sex timmar efter start av beroende identifiering.

## <a name="visualize-dependencies"></a>Visualisera beroenden

1. Klicka på **identifierade servrar**i **Azure Migrate: Server bedömning**.
2. Sök efter den dator som du vill visa.
3. I kolumnen **beroenden** klickar du på **Visa beroenden**
4. Ändra den tids period som du vill visa kartan med i list rutan **tids längd** .
5. Expandera **klient** gruppen för att visa en lista med datorer som är beroende av den valda datorn.
6. Expandera **port** gruppen om du vill visa en lista med datorer som är beroende av den valda datorn.
7. Om du vill navigera till vyn karta för någon av de beroende datorerna klickar du på dator namnet > **belastnings Server karta**

    ![Expandera server port grupp och läsa in Server karta](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Expandera klient grupp ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Expandera den valda datorn om du vill visa information på process nivå för varje beroende.

    ![Expandera Server för att visa processer](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Process information för ett beroende är inte alltid tillgängligt. Om det inte är tillgängligt visas beroendet med processen markerad som "okänd process".

## <a name="export-dependency-data"></a>Exportera beroende data

1. Klicka på **identifierade servrar**i **Azure Migrate: Server bedömning**.
2. Klicka på ikonen **beroende analys** .
3. Klicka på **Exportera program beroenden**.
4. På sidan **Exportera program beroenden** väljer du den installation som identifierar relevanta datorer.
5. Välj start tid och slut tid. Observera att du bara kan hämta data under de senaste 30 dagarna.
6. Klicka på **Exportera beroende**.

Beroende data exporteras och hämtas i CSV-format. Den hämtade filen innehåller beroende data över alla datorer som är aktiverade för beroende analys. 

![Exportera beroenden](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>Beroende information

Varje rad i den exporterade CSV-filen motsvarar ett beroende som observerats inom den angivna tids perioden. 

I följande tabell sammanfattas fälten i den exporterade CSV-filen. Observera att fälten Server namn, program och process endast fylls i för servrar som har en agent lös beroende analys aktive rad.

**Fältnamn** | **Information**
--- | --- 
Timeslot | Timeslot under vilken beroendet observerades. <br/> Beroende data samlas in över 6 timmars fack för närvarande.
Käll Server namn | Namnet på käll datorn 
Käll program | Namnet på programmet på käll datorn 
Käll process | Namnet på processen på käll datorn 
Mål server namn | Namnet på mål datorn
Mål-IP-adress | Mål datorns IP-adress
Mål program | Namnet på programmet på mål datorn
Mål process | Namnet på processen på mål datorn 
Målport | Port nummer på mål datorn


## <a name="stop-dependency-discovery"></a>Stoppa beroende identifiering

Välj de datorer där du vill stoppa beroende identifiering.

1. Klicka på **identifierade servrar**i **Azure Migrate: Server bedömning**.
2. Klicka på ikonen **beroende analys** .
3. Klicka på **ta bort servrar**.
3. På sidan **ta bort servrar** **väljer du den installation som identifierar** de virtuella datorer där du vill stoppa beroende identifiering.
4. Välj datorerna i listan dator.
5. Klicka på **ta bort servrar**.


## <a name="next-steps"></a>Nästa steg

[Gruppera datorer](how-to-create-a-group.md) för utvärdering.
