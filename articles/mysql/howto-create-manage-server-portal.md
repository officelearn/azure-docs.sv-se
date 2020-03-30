---
title: Hantera server - Azure-portal - Azure Database för MySQL
description: Lär dig hur du hanterar en Azure-databas för MySQL-server från Azure-portalen.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c0bee0b628a49746a19545d14b8b8761d0e880d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062407"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Hantera en Azure-databas för MySQL-server med Azure-portalen
Den här artikeln visar hur du hanterar din Azure-databas för MySQL-servrar. Hanteringsuppgifter omfattar beräknings- och lagringsskalning, återställning av administratörslösenord och visning av serverinformation.

## <a name="sign-in"></a>Logga in
Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-server"></a>Skapa en server
Besök [snabbstarten](quickstart-create-mysql-server-database-using-azure-portal.md) för att lära dig hur du skapar och kommer igång med en Azure-databas för MySQL-server.

## <a name="scale-compute-and-storage"></a>Skala beräkning och lagring

När du har skapat servern kan du skala mellan nivåerna Allmänt syfte och Minnesoptimerade när dina behov ändras. Du kan också skala beräkning och minne genom att öka eller minska virtuella kärnor. Lagring kan skalas upp (du kan dock inte skala ner lagringsutrymmet).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Skala mellan allmänna ändamål och minnesoptimerade nivåer

Du kan skala från allmänt syfte till minnesoptimerad och vice versa. Det går inte att ändra till och från basic-nivån när servern har skapats. 

1. Välj din server i Azure-portalen. Välj **prisnivå**, som finns i avsnittet **Inställningar.**

2. Välj **Allmänt ändamål** eller **Minnesoptimerad**, beroende på vad du skalar till. 

    ![förändringsprisnivå](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > Om du ändrar nivåer startas en serverstart.

4. Välj **OK** för att spara ändringar.


### <a name="scale-vcores-up-or-down"></a>Skala virtuella kärnor uppåt eller nedåt

1. Välj din server i Azure-portalen. Välj **prisnivå**, som finns i avsnittet **Inställningar.**

2. Ändra **vCore-inställningen** genom att flytta skjutreglaget till önskat värde.

    ![skala-beräkna](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > Skalning av virtuella kärnor gör att en server startas om.

3. Välj **OK** för att spara ändringar.


### <a name="scale-storage-up"></a>Skala lagring upp

1. Välj din server i Azure-portalen. Välj **prisnivå**, som finns i avsnittet **Inställningar.**

2. Ändra **lagringsinställningen** genom att flytta skjutreglaget till önskat värde.

    ![skala-lagring](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > Det går inte att skala ned lagringsutrymmet.

3. Välj **OK** för att spara ändringar.


## <a name="update-admin-password"></a>Uppdatera administratörslösenord
Du kan ändra administratörsrollens lösenord med Hjälp av Azure-portalen.

1. Välj din server i Azure-portalen. Välj **Återställ lösenord**i fönstret **Översikt** .

   ![översikt](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Ange ett nytt lösenord och bekräfta lösenordet. Textrutan kommer att fråga dig om krav på lösenordskomplexitet.

   ![återställa-lösenord](./media/howto-create-manage-server-portal/reset-password.png)

3. Välj **OK** om du vill spara det nya lösenordet.


## <a name="delete-a-server"></a>Ta bort en server

Du kan ta bort servern om du inte längre behöver den. 

1. Välj din server i Azure-portalen. Välj **Ta bort**i fönstret **Översikt** .

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Skriv namnet på servern i indatarutan för att bekräfta att det här är den server som du vill ta bort.

    ![bekräfta-ta bort](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > Det går inte att ta bort en server.

3. Välj **Ta bort**.


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [säkerhetskopior och serveråterställning](howto-restore-server-portal.md)
- Lär dig mer om [justerings- och övervakningsalternativ i Azure Database for MySQL](concepts-monitoring.md)
