---
title: Distribuera SAP IDES EHP7 SP3 för SAP ERP 6.0 på Azure | Microsoft-dokument
description: Distribuera SAP IDES EHP7 SP3 för SAP ERP 6.0 på Azure
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 3efd92226b7c69590f3960458ffec49b63b8364f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616692"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Distribuera SAP IDES EHP7 SP3 för SAP ERP 6.0 på Azure
I den hÃ¤r artikeln beskrivs hur du distribuerar ett SAP IDES-system som fÃ¶rsÃ¶rsÃ¶r med SQL Server och Windows-operativsystemet på Azure via SAP Cloud Appliance Library (SAP CAL) 3.0. Skärmbilderna visar steg-för-steg-processen. Om du vill distribuera en annan lösning följer du samma steg.

Till att börja med SAP CAL går du till [SAP Cloud Appliance Librarys](https://cal.sap.com/) webbplats. SAP har också en blogg om den nya [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> Från och med den 29 maj 2017 kan du använda distributionsmodellen för Azure Resource Manager utöver den klassiska distributionsmodellen som föredras för att distribuera SAP CAL. Vi rekommenderar att du använder den nya Resurshanterarens distributionsmodell och bortser från den klassiska distributionsmodellen.

Om du redan har skapat ett SAP CAL-konto som använder den klassiska modellen *måste du skapa ett annat SAP CAL-konto*. Det här kontot behöver distribueras exklusivt till Azure med hjälp av Resource Manager-modellen.

När du har loggat in på SAP CAL leder den första sidan vanligtvis dig till sidan **Lösningar.** De lösningar som erbjuds på SAP CAL ökar stadigt, så du kan behöva bläddra en hel del för att hitta den lösning du vill ha. Den markerade Windows-baserade SAP IDES-lösningen som är tillgänglig exklusivt på Azure visar distributionsprocessen:

![SAP CAL-lösningar](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Skapa ett konto i SAP CAL
1. Om du vill logga in på SAP CAL för första gången använder du din SAP S-User eller någon annan användare som är registrerad hos SAP. Definiera sedan ett SAP CAL-konto som används av SAP CAL för att distribuera enheter på Azure. I kontodefinitionen måste du:

    a. Välj distributionsmodellen på Azure (Resource Manager eller classic).

    b. Ange din Azure-prenumeration. Ett SAP CAL-konto kan endast tilldelas en prenumeration. Om du behöver mer än en prenumeration måste du skapa ett annat SAP CAL-konto.
    
    c. Ge SAP CAL-behörigheten att distribuera till din Azure-prenumeration.

   > [!NOTE]
   >  Nästa steg visar hur du skapar ett SAP CAL-konto för Resource Manager-distributioner. Om du redan har ett SAP CAL-konto som är länkat till den klassiska distributionsmodellen *måste* du följa dessa steg för att skapa ett nytt SAP CAL-konto. Det nya SAP CAL-kontot måste distribueras i Resource Manager-modellen.

1. Om du vill skapa ett nytt SAP CAL-konto visar sidan **Konton** två alternativ för Azure: 

    a. **Microsoft Azure (klassisk)** är den klassiska distributionsmodellen och är inte längre att föredra.

    b. **Microsoft Azure** är den nya resurshanterarens distributionsmodell.

    ![SAP CAL-konton](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Om du vill distribuera i Resource Manager-modellen väljer du **Microsoft Azure**.

    ![SAP CAL-konton](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Ange **Azure-prenumerations-ID** som finns på Azure-portalen. 

    ![PRENUMERATIONS-ID för SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Om du vill auktorisera SAP CAL att distribuera till den Azure-prenumeration du definierade klickar du på **Auktorisera**. Följande sida visas på webbläsarfliken:

    ![Logga in i internet explorer-molntjänster](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Om mer än en användare visas väljer du det Microsoft-konto som är länkat för att vara medadministrator för den Azure-prenumeration du valde. Följande sida visas på webbläsarfliken:

    ![Bekräftelse av molntjänster i Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Klicka på **Acceptera**. Om auktoriseringen lyckas visas SAP CAL-kontodefinitionen igen. Efter en kort tid bekräftar ett meddelande att auktoriseringsprocessen lyckades.

1. Om du vill tilldela det nyskapade SAP CAL-kontot till användaren anger du ditt **användar-ID** i textrutan till höger och klickar på **Lägg till**. 

    ![Konto till användarassociation](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Om du vill koppla ditt konto till den användare som du använder för att logga in på SAP CAL klickar du på **Granska**. 

1. Om du vill skapa kopplingen mellan användaren och det nyligen skapade SAP CAL-kontot klickar du på **Skapa**.

    ![Användare till kontoassociation](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Du har skapat ett SAP CAL-konto som kan:

- Använd Resource Manager-distributionsmodellen.
- Distribuera SAP-system till din Azure-prenumeration.

> [!NOTE]
> Innan du kan distribuera SAP IDES-lösningen baserat på Windows och SQL Server kan du behöva registrera dig för en SAP CAL-prenumeration. Annars kan lösningen visas som **låst** på översiktssidan.

### <a name="deploy-a-solution"></a>Distribuera en lösning
1. När du har konfigurerat ett SAP CAL-konto väljer du **SAP IDES-lösningen på Windows- och SQL Server-lösningen.** Klicka på **Skapa instans**och bekräfta användnings- och villkoren. 

1. På sidan **Grundläge: Skapa instans** måste du:

    a. Ange ett **förekomstnamn**.

    b. Välj en **Azure-region**. Du kan behöva en SAP CAL-prenumeration för att få flera Azure-regioner som erbjuds.

    c.  Ange **huvudlösenordet** för lösningen, som visas:

    ![SAP CAL Basic Mode: Skapa instans](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Klicka på **Skapa**. Efter en tid, beroende på lösningens storlek och komplexitet (SAP CAL ger en uppskattning), visas statusen som aktiv och klar för användning: 

    ![SAP CAL-instanser](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Om du vill hitta resursgruppen och alla dess objekt som har skapats av SAP CAL går du till Azure-portalen. Den virtuella datorn kan hittas från och med samma instansnamn som angavs i SAP CAL.

    ![Objekt för resursgrupp](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. Gå till de distribuerade instanserna på SAP CAL-portalen och klicka på **Anslut**. Följande popup-fönster visas: 

    ![Anslut till instansen](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Innan du kan använda något av alternativen för att ansluta till de distribuerade systemen klickar du på **Komma igång guide**. Dokumentationen namnger användarna för var och en av anslutningsmetoderna. Lösenorden för dessa användare är inställda på huvudlösenordet som du definierade i början av distributionsprocessen. I dokumentationen visas andra mer funktionella användare med sina lösenord, som du kan använda för att logga in på det distribuerade systemet.

    ![Dokumentation för VÄLKOMMEN TILL SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Inom några timmar distribueras ett nyttigt SAP IDES-system i Azure.

Om du har köpt en SAP CAL-prenumeration stöder SAP distributioner fullt ut via SAP CAL på Azure. Supportkön är BC-VCM-CAL.

