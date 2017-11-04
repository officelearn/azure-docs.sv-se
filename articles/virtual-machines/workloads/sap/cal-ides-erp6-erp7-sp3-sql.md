---
title: "Distribuera SAP IDES EHP7 SP3 för SAP ERP 6.0 i Azure | Microsoft Docs"
description: "Distribuera SAP IDES EHP7 SP3 för SAP ERP 6.0 på Azure"
services: virtual-machines-windows
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 91eed294077ff72d0760018b10c98f32db88f3be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Distribuera SAP IDES EHP7 SP3 för SAP ERP 6.0 på Azure
Den här artikeln beskriver hur du distribuerar ett SAP IDES system körs med SQL Server och Windows-operativsystemet på Azure via SAP-installation Molnbibliotek (SAP CAL) 3.0. Skärmbilderna visar processen. Följ samma steg för att distribuera en annan lösning.

Om du vill börja med SAP-CAL, gå till den [SAP installation Molnbibliotek](https://cal.sap.com/) webbplats. SAP har också en blogg om den nya [SAP molnet installation biblioteket 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
Du kan använda Azure Resource Manager-distributionsmodellen utöver den önskade mindre klassiska distributionsmodellen för att distribuera SAP-CAL från och med den 29 maj 2017. Vi rekommenderar att du använder den nya Resource Manager-distributionsmodellen och bortse från den klassiska distributionsmodellen.

Om du redan skapat ett SAP CAL-konto som använder den klassiska modellen *måste du skapa en annan SAP CAL-konto*. Det här kontot måste distribueras enbart till Azure med hjälp av Resource Manager-modellen.

När du loggar in på SAP-CAL, första sidan vanligtvis leder dig till den **lösningar** sidan. Lösningar som erbjuds via SAP-CAL ökar stadigt, så du kanske måste rulla ganska lite för att hitta den lösning som du vill använda. Den markerade Windows-baserade SAP IDES lösning som är tillgänglig enbart på Azure visar distributionsprocessen:

![SAP CAL-lösningar](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Skapa ett konto i SAP-CAL
1. Om du vill logga in till SAP-CAL för första gången använder din SAP-S-användare eller en annan användare har registrerat hos SAP. Definiera ett SAP CAL-konto som används av SAP-CAL för att distribuera installationer på Azure. I definitionen konto måste du:

    a. Välj distributionsmodell i Azure (Resource Manager eller klassisk).

    b. Ange din Azure-prenumeration. En SAP CAL-konto kan tilldelas till en prenumeration. Om du behöver mer än en prenumeration måste du skapa en annan SAP CAL-konto.
    
    c. Ge SAP CAL-behörighet att distribuera till din Azure-prenumeration.

    > [!NOTE]
    Nästa steg visar hur du skapar ett SAP CAL-konto för Resource Manager distributioner. Om du redan har ett SAP CAL-konto som är länkad till den klassiska distributionsmodellen du *måste* att följa dessa steg om du vill skapa ett nytt SAP CAL-konto. Det nya SAP CAL-kontot måste distribueras i Resource Manager-modellen.

2. Skapa ett nytt SAP CAL-konto i **konton** sidan visas två alternativ för Azure: 

    a. **Microsoft Azure (klassisk)** är den klassiska distributionsmodellen och är inte längre önskade.

    b. **Microsoft Azure** är den nya Resource Manager-distributionsmodellen.

    ![SAP CAL-konton](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Om du vill distribuera i Resource Manager-modellen, Välj **Microsoft Azure**.

    ![SAP CAL-konton](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

3. Ange Azure **prenumerations-ID** som finns på Azure-portalen. 

    ![SAP CAL prenumerations-ID](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

4. För att godkänna SAP-CAL att distribuera till Azure-prenumerationen du definierat klickar **auktorisera**. Följande sida visas i fliken i webbläsaren:

    ![Internet Explorer cloud services-inloggning](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

5. Om fler än en användare visas väljer du Microsoft-kontot som är kopplad till att coadministrator av Azure-prenumerationen du valt. Följande sida visas i fliken i webbläsaren:

    ![Bekräftelse för Internet Explorer cloud services](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

6. Klicka på **acceptera**. Om tillståndet lyckas visar definition för SAP CAL-konto igen. Efter en kort tid ett meddelande som bekräftar att Auktoriseringen lyckades.

7. Om du vill tilldela användaren det nyligen skapade SAP CAL-kontot, ange din **användar-ID** i rutan till höger och klicka på **Lägg till**. 

    ![Kontot till användarassociationen](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

8. Om du vill koppla ditt konto till den användare som du använder för att logga in på SAP-CAL, klickar du på **granska**. 

9. Om du vill skapa kopplingen mellan användaren och det nyligen skapade SAP CAL-kontot, klickar du på **skapa**.

    ![Användaren kontokoppling](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Du har skapat ett SAP CAL-konto som kan:

- Använd Resource Manager-distributionsmodellen.
- Distribuera SAP-system i din Azure-prenumeration.

> [!NOTE]
Innan du kan distribuera lösningen SAP IDES baserat på Windows och SQL Server, kan du behöva registrera dig för en SAP CAL-prenumeration. I annat fall lösningen kan visas som **låst** på översiktssidan.

### <a name="deploy-a-solution"></a>Distribuera en lösning
1. När du har skapat ett SAP CAL-konto, Välj **i SAP IDES lösning på Windows och SQL Server** lösning. Klicka på **skapa instans**, och kontrollera villkoren för användning och villkor. 

2. På den **grundläggande läge: skapa instans** sidan som du behöver:

    a. Ange en instans **namn**.

    b. Välj en Azure **Region**. Du kan behöva en SAP CAL-prenumeration för att få flera Azure-regioner som erbjuds.

    c.  Ange huvudservern **lösenord** för lösning, som visas:

    ![SAP CAL grundläggande läge: Skapa en instans](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

3. Klicka på **Skapa**. Efter en stund, beroende på storleken och komplexiteten för lösning (SAP-CAL ger en uppskattning), visas status som aktiv och redo att användas: 

    ![SAP CAL-instanser](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

4. Gå till Azure-portalen för att hitta resursgruppen och alla objekt som har skapats av SAP-klientåtkomstlicens. Den virtuella datorn finns från och med samma instansnamn som gavs i SAP-klientåtkomstlicens.

    ![Resursen gruppobjekt](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

5. Gå till distribuerade instanser på SAP CAL-portalen och på **Anslut**. Följande popup-fönstret visas: 

    ![Anslut till instansen](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

6. Innan du kan använda något av alternativen för att ansluta till distribuerade system, klickar du på **komma igång**. I dokumentationen för att användarna för varje anslutningsmetoder. Lösenorden för dessa användare har angetts till master lösenordet som du angav i början av distributionsprocessen. I dokumentationen för visas andra mer funktionell användare med sina lösenord, som du kan använda för att logga in på det distribuerade systemet.

    ![Välkommen till SAP-dokumentation](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

En felfri systemet SAP IDES distribueras inom några timmar i Azure.

Om du har köpt en prenumeration för SAP CAL stöd SAP fullständigt för distribution via SAP-CAL på Azure. Stöd för kön är BC-VCM-klientåtkomstlicens.

