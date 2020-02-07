---
title: Gruppera datorer i Azure Migrate med hjälp av en agent utan agent beroende visualisering
description: Beskriver hur du skapar grupper med hjälp av dator beroenden på ett agent effektivt sätt.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: hamusa
ms.openlocfilehash: c8ddd343cd00b24506382521361ebad33ad112a7
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049754"
---
# <a name="set-up-agentless-dependency-visualization-for-assessment"></a>Konfigurera en agent utan beroende visualisering för utvärdering

Den här artikeln beskriver hur du ställer in en agent lös beroende mappning i Azure Migrate: Server bedömning. 

> [!IMPORTANT]
> Det finns för närvarande en för hands version av en beroende visualisering för virtuella Azure VMware-datorer som identifieras med en Azure Migrate-apparat.
> Vissa funktioner kanske inte stöds eller kan vara begränsade. Den här för hands versionen täcks av kund support och kan användas för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="about-dependency-mapping"></a>Om beroende mappning

Beroende mappning hjälper dig att visualisera beroenden mellan datorer som du vill utvärdera och migrera. Du använder vanligt vis beroende mappning när du vill utvärdera datorer med högre Tillförlitlighets nivåer.

- I Azure Migrate: Server utvärdering samlar du ihop datorer i grupper för utvärdering. Grupper består vanligt vis av datorer som du vill migrera tillsammans, och beroende mappningen hjälper dig att kontrol lera maskin beroenden, så att du kan gruppera datorerna korrekt.
- Med hjälp av mappning kan du identifiera beroende system som behöver migreras tillsammans. Du kan också identifiera om ett system som körs fortfarande betjänar användare eller är en kandidat för inaktive ring i stället för migrering.
- Visualisering av beroenden hjälper till att se till att ingenting är kvar bakom och Undvik oväntade avbrott under migreringen.

## <a name="about-agentless-visualization"></a>Om agent utan visualisering

Visualisering av beroenden för agenter kräver inte att du installerar några agenter på datorer. Det fungerar genom att samla in data för TCP-anslutningen från datorer som den är aktive rad för.

- När beroende identifiering har startats samlar enheten in data från datorer vid ett avsöknings intervall på fem minuter.
- Följande data samlas in:
    - TCP-anslutningar
    - Namn på processer som har aktiva anslutningar
    - Namn på installerade program som kör ovanstående processer
    - Nej. anslutningar som identifieras vid varje avsöknings intervall

## <a name="current-limitations"></a>Aktuella begränsningar

- Visualisering av beroende för virtuella VMware-datorer är för närvarande endast tillgängligt för virtuella VMware-datorer.
- Just nu kan du inte lägga till eller ta bort en server från en grupp i vyn beroende analys.
- Beroende karta för en Server grupp är för närvarande inte tillgänglig.
- Beroende data kan för närvarande inte hämtas i tabell format.

## <a name="before-you-start"></a>Innan du börjar

- Se till att du har [skapat](how-to-add-tool-first-time.md) ett Azure Migrate-projekt.
- Agentens beroende analys är för närvarande endast tillgänglig för VMware-datorer.
- Om du redan har skapat ett projekt kontrollerar du att du har [lagt till](how-to-assess.md) Azure Migrate: Server utvärderings verktyget.
- Kontrol lera att du har identifierat VMware-datorerna i Azure Migrate; Det kan du göra genom att konfigurera en Azure Migrate-installation för [VMware](how-to-set-up-appliance-vmware.md). Enheten identifierar lokala datorer och skickar metadata-och prestanda data till Azure Migrate: Server utvärdering. [Läs mer](migrate-appliance.md).
- [Granska kraven](migrate-support-matrix-vmware.md#agentless-dependency-visualization) för att skapa en agent utan beroende visualisering.



## <a name="create-a-user-account-for-discovery"></a>Skapa ett användar konto för identifiering

Konfigurera ett användar konto som har de behörigheter som krävs så att Server utvärderingen kan komma åt den virtuella datorn för identifiering. Du kan ange ett användar konto.

- **Nödvändig behörighet för virtuella Windows-datorer**: användar kontot måste vara lokalt eller ha en domän administratör.
- **Nödvändig behörighet för virtuella Linux-datorer**: rot privilegiet krävs för kontot. Alternativt måste användar kontot ha dessa två funktioner på/bin/netstat-och/bin/ls-filer: CAP_DAC_READ_SEARCH och CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>Lägg till användar kontot till enheten

Du måste lägga till användar kontot till-enheten.

Lägg till kontot enligt följande:

1. Öppna appen för hantering av appar. Navigera till panelen **Tillhandahåll vCenter-information** .
2. I avsnittet **identifiera program och beroenden för virtuella datorer** klickar du på **Lägg till autentiseringsuppgifter**
3. Välj **operativ system**.
4. Ange ett eget namn för kontot.
5. Ange **användar namn** och **lösen ord**
6. Klicka på **Save** (Spara).
7. Klicka på **Spara och starta identifiering**.

    ![Lägg till användar konto för virtuell dator](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Starta beroende identifiering

Välj de datorer där du vill aktivera beroende identifiering.

1. Klicka på **identifierade servrar**i **Azure Migrate: Server bedömning**.
2. Klicka på ikonen **beroende analys** .
3. Klicka på **Lägg till servrar**.
3. På sidan **Lägg till servrar** väljer du den installation som identifierar relevanta datorer.
4. Välj datorerna i listan dator.
5. Klicka på **Lägg till servrar**.

    ![Starta beroende identifiering](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Du kommer att kunna visualisera beroenden 6 timmar efter start av beroende identifiering.

## <a name="visualize-dependencies"></a>Visualisera beroenden

1. Klicka på **identifierade servrar**i **Azure Migrate: Server bedömning**.
2. Sök efter den dator som du vill visa beroende kartan för.
3. Klicka på **Visa beroenden** i kolumnen **beroenden** .
4. Ändra den tids period som du vill visa kartan med i list rutan **tids längd** .
5. Expandera **klient** gruppen för att visa en lista över datorer som har ett beroende på den valda datorn.
6. Expandera **port** gruppen om du vill visa en lista med datorer som är beroende av den valda datorn.
7. Om du vill navigera till vyn karta för någon av de beroende datorerna klickar du på dator namnet och klickar sedan på **Läs in Server karta**

    ![Expandera server port grupp och läsa in Server karta](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Expandera klient grupp ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Expandera den valda datorn om du vill visa information på process nivå för varje beroende.

    ![Expandera Server för att visa processer](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Process information för ett beroende är inte alltid tillgängligt. Om det inte är tillgängligt visas beroendet med processen markerad som "okänd process".

## <a name="stop-dependency-discovery"></a>Stoppa beroende identifiering

Välj de datorer där du vill stoppa beroende identifiering.

1. Klicka på **identifierade servrar**i **Azure Migrate: Server bedömning**.
2. Klicka på ikonen **beroende analys** .
3. Klicka på **ta bort servrar**.
3. På sidan **ta bort servrar** **väljer du den installation som identifierar** de virtuella datorer där du vill stoppa beroende identifiering.
4. Välj datorerna i listan dator.
5. Klicka på **ta bort servrar**.


## <a name="next-steps"></a>Nästa steg

[Gruppera datorerna](how-to-create-a-group.md)
