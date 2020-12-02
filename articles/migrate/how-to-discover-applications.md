---
title: Identifiera appar, roller och funktioner på lokala servrar med Azure Migrate
description: Lär dig hur du identifierar appar, roller och funktioner på lokala servrar med Azure Migrate Server bedömning.
ms.topic: article
ms.date: 06/10/2020
ms.openlocfilehash: 960877ac4eab4d8f23b5616dc04628142964b46a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483461"
---
# <a name="discover-machine-apps-roles-and-features"></a>Identifiera appar, roller och funktioner i datorn

I den här artikeln beskrivs hur du identifierar program, roller och funktioner på lokala servrar med hjälp av Azure Migrate: Server bedömning.

Identifiering av inventering av appar, roller och funktioner som körs på lokala datorer hjälper till att identifiera och skräddarsy en migrerings Sök väg till Azure för dina arbets belastningar. App-Discovery använder Azure Migrate-installationen för att utföra identifieringen med hjälp av autentiseringsuppgifter för virtuella gäst datorer. App-Discovery är agent utan agent. Ingenting installeras på virtuella datorer.

> [!NOTE]
> App Discovery är för närvarande endast för för hands versioner för virtuella VMware-datorer och är begränsat till enbart identifiering. Vi erbjuder ännu inte app-baserad utvärdering. 


## <a name="before-you-start"></a>Innan du börjar

- Kontrollera att du har:
    - [Skapat](./create-manage-projects.md) ett Azure Migrate-projekt.
    - [Lade](how-to-assess.md) till verktyget Azure Migrate: Server utvärderings verktyg i ett projekt.
- Granska [support och krav för app-Discovery](migrate-support-matrix-vmware.md#vmware-requirements).
- Se till att de virtuella datorerna där du kör app-Discovery har PowerShell version 2,0 eller senare installerat och att VMware-verktyg (senare än 10.2.0) är installerat.
- Kontrol lera [kraven](migrate-appliance.md) för att distribuera Azure Migrate-enheten.


## <a name="deploy-the-azure-migrate-appliance"></a>Distribuera Azure Migrate-apparaten

1. [Granska](migrate-appliance.md#appliance---vmware) kraven för att distribuera Azure Migrate-enheten.
2. Granska de Azure-URL: er som krävs för att få åtkomst till [molnet](migrate-appliance.md#government-cloud-urls) [offentliga](migrate-appliance.md#public-cloud-urls) och myndigheter.
3. [Granska data](migrate-appliance.md#collected-data---vmware) som samlas in under identifiering och bedömning.
4. [Antecknings](migrate-support-matrix-vmware.md#port-access-requirements) portens åtkomst krav för produkten.
5. [Distribuera Azure Migrate-apparaten](how-to-set-up-appliance-vmware.md) för att starta identifieringen. För att distribuera installationen kan du hämta och importera en ägg-mall till VMware för att skapa installationen som en virtuell VMware-dator. Du konfigurerar installationen och registrerar den sedan med Azure Migrate.
6. När du distribuerar installationen kan du starta kontinuerlig identifiering genom att ange följande:
    - Namnet på vCenter Server som du vill ansluta till.
    - Autentiseringsuppgifter som du har skapat för installationen av för att ansluta till vCenter Server.
    - De kontoautentiseringsuppgifter som du skapade för installationen av för att ansluta till virtuella Windows-och Linux-datorer.

När installationen har distribuerats och du har angett autentiseringsuppgifter, startar installationen kontinuerlig identifiering av VM-metadata och prestanda data, tillsammans med och identifiering av appar, funktioner och roller.  Längden på appens identifiering är beroende av hur många virtuella datorer du har. Det tar vanligt vis en timme för app-identifiering av 500 virtuella datorer.

## <a name="verify-permissions"></a>Kontrollera behörigheter

Du har [skapat ett vCenter Server skrivskyddat konto](./tutorial-discover-vmware.md#prepare-vmware) för identifiering och utvärdering. Det skrivskyddade kontot måste ha behörighet för **Virtual Machines**  >  **gäst åtgärder** för att kunna interagera med den virtuella datorn för identifiering av appar.

### <a name="add-the-user-account-to-the-appliance"></a>Lägg till användar kontot till enheten

Lägg till användar kontot enligt följande:

1. Öppna appen för hantering av appar. 
2. Navigera till panelen **Tillhandahåll vCenter-information** .
3. I **identifiera program och beroenden på virtuella datorer** klickar du på **Lägg till autentiseringsuppgifter**
3. Välj **operativ system**, ange ett eget namn för kontot och **User name** / **lösen ordet** för användar namn
6. Klicka på **Spara**.
7. Klicka på **Spara och starta identifiering**.

    ![Lägg till användar konto för virtuell dator](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>Granska och exportera inventeringen

Om du har angett autentiseringsuppgifter för identifiering av appar när identifieringen har slutförts, kan du granska och exportera program inventeringen i Azure Portal.

1. I **Azure Migrate-servrar**  >  **Azure Migrate: Server utvärdering** klickar du på det visade antalet för att öppna sidan **identifierade servrar** .

    > [!NOTE]
    > I det här skedet kan du också ställa in beroende analys för identifierade datorer, så att du kan visualisera beroenden mellan datorer som du vill utvärdera. [Läs mer](concepts-dependency-visualization.md) om beroende analys.

2. I **program som identifierats** klickar du på det visade antalet.
3. I **program inventering** kan du granska identifierade appar, roller och funktioner.
4. Exportera inventeringen genom att klicka på **Exportera app Inventory** i **identifierade servrar**.

App-inventeringen exporteras och hämtas i Excel-format. **Program inventerings** bladet visar alla appar som identifierats på alla datorer.

## <a name="next-steps"></a>Nästa steg

- [Skapa en utvärdering](how-to-create-assessment.md) för identifierade servrar.
- Utvärdera SQL Server databaser med [Azure Migrate: databas utvärdering](/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).