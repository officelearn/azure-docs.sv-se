---
title: Upptäck appar, roller och funktioner på lokala servrar med Azure Migrate
description: Lär dig hur du identifierar appar, roller och funktioner på lokala servrar med Azure Migrate Server Assessment.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453590"
---
# <a name="discover-machine-apps-roles-and-features"></a>Identifiera maskinappar, roller och funktioner

I den hÃ¤r artikeln beskrivs hur du identifierar program, roller och funktioner på lokala servrar med Azure Migrate: Server Assessment.

Genom att identifiera inventeringen av appar och roller/funktioner som körs på dina lokala datorer kan du identifiera och planera en migreringsväg till Azure som är skräddarsydd för dina arbetsbelastningar.

> [!NOTE]
> Appidentifiering är för närvarande endast i förhandsversion för virtuella datorer med VMware och är begränsad till identifiering. Vi erbjuder ännu inte appbaserad bedömning. Maskinbaserad bedömning för lokala virtuella datorer, virtuella hyper-v-datorer och fysiska servrar.

Appidentifiering med Azure Migrate: Serverutvärdering är agentlös. Ingenting är installerat på maskiner och virtuella datorer. Serverutvärdering använder Azure Migrate-enheten för att utföra identifiering tillsammans med datorgästautentiseringsuppgifter. Apparaten får fjärråtkomst till VMware-maskinerna med hjälp av VMware-API:er.


## <a name="before-you-start"></a>Innan du börjar

1. Kontrollera att du har [skapat](how-to-add-tool-first-time.md) ett Azure Migrate-projekt.
2. Kontrollera att du har [lagt till](how-to-assess.md) verktyget Azure Migrate: Server Assessment i ett projekt.
4. Kontrollera [VMware-kraven](migrate-support-matrix-vmware.md#vmware-requirements) för att upptäcka och bedöma virtuella datorer med Azure Migrate-enheten.
5. Kontrollera [kraven](migrate-appliance.md) för distribution av Azure Migrate-installationen.
6. [Verifiera support och krav](migrate-support-matrix-vmware.md#application-discovery) för programidentifiering.

## <a name="prepare-for-app-discovery"></a>Förbered för appidentifiering

1. [Förbered för distribution av apparaten](tutorial-prepare-vmware.md). Förberedelse inkluderar att verifiera inställningarna för installationen och att konfigurera ett konto som enheten använder för att komma åt vCenter Server.
2. Kontrollera att du har ett användarkonto (ett vardera för Windows- och Linux-servrar) med administratörsbehörighet för datorer där du vill identifiera appar, roller och funktioner.
3. [Distribuera Azure Migrate-enheten](how-to-set-up-appliance-vmware.md) för att starta identifieringen. Om du vill distribuera apparaten hämtar och importerar du en OVA-mall till VMware för att skapa installationen som en virtuell virtuell VMware-dator. Du konfigurerar installationen och registrerar den sedan med Azure Migrate.
2. När du distribuerar enheten anger du följande för att starta kontinuerlig identifiering:
    - Namnet på den vCenter-server som du vill ansluta till.
    - Autentiseringsuppgifter som du har skapat för att installationen ska ansluta till vCenter Server.
    - Kontoautentiseringsuppgifterna som du skapade för att installationen ska ansluta till virtuella datorer med Windows/Linux.

När installationen har distribuerats och du har angett autentiseringsuppgifter startar installationen kontinuerlig identifiering av VM-metadata och prestandadata, tillsammans med och identifiering av appar, funktioner och roller.  Hur länge appidentifiering beror på hur många virtuella datorer du har. Det tar vanligtvis en timme för appidentifiering av 500 virtuella datorer.

## <a name="review-and-export-the-inventory"></a>Granska och exportera lagret

När identifieringen har avslutats, om du har angett autentiseringsuppgifter för appidentifiering, kan du granska och exportera appinventeringen i Azure-portalen.

1. I **Azure Migrate - Servrar** > **Azure Migrera: Serverutvärdering**klickar du på det antal som visas för att öppna sidan **Identifierade servrar.**

    > [!NOTE]
    > I det här skedet kan du också konfigurera beroendemappning för identifierade datorer, så att du kan visualisera beroenden mellan datorer som du vill bedöma. [Läs mer](how-to-create-group-machine-dependencies.md).

2. I **Program som upptäcktes**klickar du på antalet visade.
3. I **Programinventering**kan du granska de identifierade apparna, rollerna och funktionerna.
4. Om du vill exportera lagret klickar du på **Exportera applager**i **Identifierade servrar.**

Applagret exporteras och hämtas i Excel-format. **I programlagerbladet** visas alla appar som har identifierats på alla datorer.

## <a name="next-steps"></a>Nästa steg

- [Skapa en bedömning](how-to-create-assessment.md) för lyft- och skiftmigrering av de identifierade servrarna.
- Utvärdera en SQL Server-databaser med [Azure Migrate: Database Assessment](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
