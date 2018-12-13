---
title: Distribuera SAP IDES EHP7 SP3 för SAP ERP 6.0 på Azure | Microsoft Docs
description: Distribuera SAP IDES EHP7 SP3 för SAP ERP 6.0 på Azure
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: eb5d731fbef22c70aaf970bf64aa90361e47a542
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53194206"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Distribuera SAP IDES EHP7 SP3 för SAP ERP 6.0 på Azure
Den här artikeln beskriver hur du distribuerar en SAP IDES datorn som kör SQL Server och operativsystemet Windows Azure via SAP Cloud Appliance Library (SAP CAL) 3.0. Skärmbilderna visar processen. Följ samma steg för att distribuera en annan lösning.

Starta med SAP CAL, gå till den [SAP Cloud Appliance Library](https://cal.sap.com/) webbplats. SAP har också en blogg om nya [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> Du kan använda Azure Resource Manager-distributionsmodellen utöver den mindre prioriterad klassiska distributionsmodellen från och med den 29 maj 2017 för att distribuera SAP CAL. Vi rekommenderar att du använder den nya Resource Manager-distributionsmodellen och bortse från den klassiska distributionsmodellen.

Om du redan skapat ett SAP CAL-konto som använder den klassiska modellen *måste du skapa ett annat SAP CAL-konto*. Det här kontot måste exklusivt distribuera till Azure med hjälp av Resource Manager-modellen.

När du har loggat in till SAP CAL första sidan vanligtvis leder dig till den **lösningar** sidan. Lösningar som erbjuds på SAP CAL ökar stadigt, så du kan behöva rulla ökar en hel del för att hitta den lösning som du vill. Den markerade Windows-baserade SAP IDES lösning som är tillgängliga enbart på Azure visar distributionsprocessen:

![SAP CAL-lösningar](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Skapa ett konto på SAP CAL
1. Registrerad med SAP vill logga in på SAP CAL för första gången ska du använda din SAP-S-användare eller en annan användare. Definiera ett SAP CAL-konto som används av SAP CAL för att distribuera enheter på Azure. I definitionen av konto måste du:

    a. Välj distributionsmodell på Azure (Resource Manager eller klassisk).

    b. Ange din Azure-prenumeration. Ett SAP CAL-konto kan tilldelas till en prenumeration. Om du behöver mer än en prenumeration kan behöva du skapa ett annat SAP CAL-konto.
    
    c. Ge SAP CAL-behörighet för att distribuera till din Azure-prenumeration.

    > [!NOTE]
    Nästa steg visar hur du skapar ett SAP CAL-konto för Resource Manager-distributioner. Om du redan har ett SAP CAL-konto som är länkad till den klassiska distributionsmodellen du *behöver* att följa stegen nedan för att skapa ett nytt SAP CAL-konto. Nytt SAP CAL-konto måste du distribuerar i Resource Manager-modellen.

1. Att skapa en ny SAP CAL-konto, den **konton** sidan visas två alternativ för Azure: 

    a. **Microsoft Azure (klassisk)** är den klassiska distributionsmodellen och är inte längre att föredra.

    b. **Microsoft Azure** är den nya Resource Manager-distributionsmodellen.

    ![SAP CAL-konton](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Om du vill distribuera i Resource Manager-modellen, Välj **Microsoft Azure**.

    ![SAP CAL-konton](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Ange Azure **prenumerations-ID** som finns på Azure portal. 

    ![SAP CAL prenumerations-ID](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. För att auktorisera SAP CAL att distribuera till Azure-prenumeration du har definierat, klickar du på **auktorisera**. Följande sida visas i fliken i webbläsaren:

    ![Logga in för Internet Explorer cloud services](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Om fler än en användare visas i listan, väljer du Microsoft-konto som är länkad till vara medadministratör för Azure-prenumeration du valde. Följande sida visas i fliken i webbläsaren:

    ![Bekräftelse för Internet Explorer cloud services](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Klicka på **acceptera**. Om auktoriseringen är klar, visar SAP CAL-konto definitionen igen. Om en stund bekräftar ett meddelande att auktoriseringsprocessen lyckades.

1. Om du vill tilldela det nyligen skapade SAP CAL-kontot till dina användare, ange din **användar-ID** i rutan till höger och klicka på **Lägg till**. 

    ![Kontot till användarassociationen](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Om du vill koppla ditt konto med användaren som används för att logga in på SAP CAL, klickar du på **granska**. 

1. Om du vill skapa associationen mellan användaren och det nyligen skapade SAP CAL-kontot, klickar du på **skapa**.

    ![Användaren kontoassociation](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Du skapat har ett SAP CAL-konto som kan:

- Använda Resource Manager-distributionsmodellen.
- Distribuera SAP-system i din Azure-prenumeration.

> [!NOTE]
Innan du kan distribuera SAP IDES lösningen bygger på Windows och SQL Server, kan du behöva registrera dig för en SAP CAL-prenumeration. I annat fall lösningen kan visas som **låst** på översiktssidan.

### <a name="deploy-a-solution"></a>Distribuera en lösning
1. När du har konfigurerat ett SAP CAL-konto, Välj **The SAP IDES lösning på Windows och SQL Server** lösning. Klicka på **skapa instans**, och bekräfta användning och allmänna villkor. 

1. På den **grundläggande läge: Skapa instans** sidan som du behöver:

    a. Ange en instans **namn**.

    b. Välj en Azure **Region**. Du kanske behöver en SAP CAL-prenumeration för att få flera Azure-regioner som erbjuds.

    c.  Ange huvudservern **lösenord** för lösningen, som visas:

    ![SAP CAL Basic-läge: Skapa instans](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Klicka på **Skapa**. Efter en stund, beroende på storleken och komplexiteten för lösningen (SAP CAL ger en beräkning) och visas status som aktiv och redo att användas: 

    ![SAP CAL-instanser](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Gå till Azure-portalen för att hitta resursgruppen och alla objekt som har skapats av SAP CAL. Den virtuella datorn finns från och med samma instansnamn som gavs i SAP CAL.

    ![Resursen gruppobjekt](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. Gå till distribuerade instanser i SAP CAL-portal och klicka **Connect**. Följande popup-fönster visas: 

    ![Anslut till instansen](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Innan du kan använda något av alternativen för att ansluta till de distribuerade system, klickar du på **Kom igång-guiden**. Dokumentationen namn användarna för var och en av metoderna anslutning. Lösenorden för dessa användare är inställda på master lösenordet som du angav i början av distributionsprocessen. I dokumentationen listas andra mer funktionell användare med sina lösenord, som du kan använda för att logga in på det distribuerade systemet.

    ![Välkommen till SAP-dokumentationen](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Inom några timmar distribueras en felfri SAP IDES systemet i Azure.

Om du har köpt en prenumeration på SAP CAL stöd SAP fullständigt för distribution via SAP CAL på Azure. Stöd för kön är BC-VCM-CAL.

