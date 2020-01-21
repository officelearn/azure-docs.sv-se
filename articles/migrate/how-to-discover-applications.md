---
title: Identifiera appar, roller och funktioner på lokala servrar med Azure Migrate
description: Lär dig hur du identifierar appar, roller och funktioner på lokala servrar med Azure Migrate Server bedömning.
ms.topic: article
ms.date: 11/20/2019
ms.openlocfilehash: 454d70a53c3ea00a4dc592703c0bc04d5517b97f
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2020
ms.locfileid: "76280772"
---
# <a name="discover-machine-apps-roles-and-features"></a>Identifiera appar, roller och funktioner i datorn

I den här artikeln beskrivs hur du identifierar program, roller och funktioner på lokala servrar med hjälp av Azure Migrate: Server bedömning.

Att identifiera inventeringen av appar, och roller/funktioner som körs på dina lokala datorer hjälper dig att identifiera och planera en migrerings Sök väg till Azure som är skräddarsydd för dina arbets belastningar.

> [!NOTE]
> Identifiering av appar stöds för närvarande endast för virtuella VMware-datorer och är begränsat till enbart identifiering. Vi erbjuder ännu inte app-baserad utvärdering. Dator-baserad utvärdering för lokala virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar.

Identifiering av app med Azure Migrate: Server utvärderingen är agent lös. Inget måste installeras på datorer och virtuella datorer. Server utvärderingen använder Azure Migrate-installationen för att utföra identifiering tillsammans med autentiseringsuppgifter för dator gäst. Enheten ansluter till VMware-datorerna via VMware-API: er.


## <a name="before-you-start"></a>Innan du börjar

1. Se till att du har [skapat](how-to-add-tool-first-time.md) ett Azure Migrate-projekt.
2. Kontrol lera att du har [lagt](how-to-assess.md) till verktyget Azure Migrate: Server utvärderings verktyg i ett projekt.
4. Kontrol lera [VMware-kraven](migrate-support-matrix-vmware.md#vmware-requirements) för att identifiera och utvärdera virtuella VMware-datorer med Azure Migrate-installationen.
5. Kontrol lera [kraven](migrate-appliance.md) för att distribuera Azure Migrate-enheten.
6. [Verifiera support och krav](/migrate-support-matrix-vmware.md#application-discovery) för program identifiering.

## <a name="prepare-for-app-discovery"></a>Förbered för identifiering av appar

1. [Förbered för distribution av installationer](tutorial-prepare-vmware.md). Förberedelsen omfattar att verifiera inställningar för enheten och att konfigurera ett konto som ska användas för att få åtkomst till vCenter Server.
2. Se till att du har ett användar konto (ett för Windows-och Linux-servrar) med administratörs behörighet för datorer där du vill identifiera appar, roller och funktioner.
3. [Distribuera Azure Migrate-apparaten](how-to-set-up-appliance-vmware.md) för att starta identifieringen. För att distribuera installationen kan du hämta och importera en ägg-mall till VMware för att skapa installationen som en virtuell VMware-dator. Du konfigurerar installationen och registrerar den sedan med Azure Migrate.
2. När du distribuerar installationen kan du starta kontinuerlig identifiering genom att ange följande:
    - Namnet på vCenter Server som du vill ansluta till.
    - Autentiseringsuppgifter som du har skapat för installationen av för att ansluta till vCenter Server.
    - De kontoautentiseringsuppgifter som du skapade för installationen av för att ansluta till virtuella Windows-och Linux-datorer.

När installationen har distribuerats och du har angett autentiseringsuppgifter, startar installationen kontinuerlig identifiering av VM-metadata och prestanda data, tillsammans med och identifiering av appar, funktioner och roller.  Längden på appens identifiering är beroende av hur många virtuella datorer du har. Det tar vanligt vis en timme för app-identifiering av 500 virtuella datorer.

## <a name="review-and-export-the-inventory"></a>Granska och exportera inventeringen

Om du har angett autentiseringsuppgifter för identifiering av appar när identifieringen är klar kan du granska och exportera program inventeringen i Azure Portal.

1. I **Azure Migrate-servrar** > **Azure Migrate: Server utvärdering**klickar du på det visade antalet för att öppna sidan **identifierade servrar** .

    > [!NOTE]
    > I det här skedet kan du också konfigurera beroende mappning för identifierade datorer, så att du kan visualisera beroenden mellan datorer som du vill utvärdera. [Läs mer](how-to-create-group-machine-dependencies.md).

2. I **program som identifierats**klickar du på det visade antalet.
3. I **program inventering**kan du granska identifierade appar, roller och funktioner.
4. Exportera inventeringen genom att klicka på **Exportera app Inventory**i **identifierade servrar**.

App-inventeringen exporteras och hämtas i Excel-format. **Program inventerings** bladet visar alla appar som identifierats på alla datorer.

## <a name="next-steps"></a>Nästa steg

- [Skapa en utvärdering](how-to-create-assessment.md) av migreringen och flyttningen av de identifierade servrarna.
- Utvärdera en SQL Server databaser med [Azure Migrate: databas utvärdering](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
