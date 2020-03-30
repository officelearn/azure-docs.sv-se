---
title: Distribuera SAP S/4HANA eller BW/4HANA på en virtuell Azure-dator | Microsoft-dokument
description: Distribuera SAP S/4HANA eller BW/4HANA på en virtuell Azure-dator
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: c110a4e0429ba52e01c472097a2241f91d504cf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616213"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Distribuera SAP S/4HANA eller BW/4HANA på Azure
I den här artikeln beskrivs hur du distribuerar S/4HANA på Azure med hjälp av SAP Cloud Appliance Library (SAP CAL) 3.0. Så här distribuerar du andra SAP HANA-baserade lösningar, till exempel BW/4HANA, gör samma steg.

> [!NOTE]
> Mer information om SAP CAL finns på webbplatsen [för SAP Cloud Appliance Library.](https://cal.sap.com/) SAP har också en blogg om [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> Från och med den 29 maj 2017 kan du använda distributionsmodellen för Azure Resource Manager utöver den klassiska distributionsmodellen som föredras för att distribuera SAP CAL. Vi rekommenderar att du använder den nya Resurshanterarens distributionsmodell och bortser från den klassiska distributionsmodellen.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Steg-för-steg-process för att distribuera lösningen

Följande sekvens av skärmbilder visar hur du distribuerar S/4HANA på Azure med hjälp av SAP CAL. Processen fungerar på samma sätt för andra lösningar, såsom BW/4HANA.

Sidan **Lösningar** visar några av de SAP CAL HANA-baserade lösningar som finns tillgängliga på Azure. **SAP S/4HANA 1610 FPS01, fullt aktiverad apparat** är i mitten raden:

![SAP CAL-lösningar](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Skapa ett konto i SAP CAL
1. Om du vill logga in på SAP CAL för första gången använder du din SAP S-User eller någon annan användare som är registrerad hos SAP. Definiera sedan ett SAP CAL-konto som används av SAP CAL för att distribuera enheter på Azure. I kontodefinitionen måste du:

    a. Välj distributionsmodellen på Azure (Resource Manager eller classic).

    b. Ange din Azure-prenumeration. Ett SAP CAL-konto kan endast tilldelas en prenumeration. Om du behöver mer än en prenumeration måste du skapa ett annat SAP CAL-konto.

    c. Ge SAP CAL-behörigheten att distribuera till din Azure-prenumeration.

   > [!NOTE]
   >  Nästa steg visar hur du skapar ett SAP CAL-konto för Resource Manager-distributioner. Om du redan har ett SAP CAL-konto som är länkat till den klassiska distributionsmodellen *måste* du följa dessa steg för att skapa ett nytt SAP CAL-konto. Det nya SAP CAL-kontot måste distribueras i Resource Manager-modellen.

1. Skapa ett nytt SAP CAL-konto. Sidan **Konton** visar tre alternativ för Azure: 

    a. **Microsoft Azure (klassisk)** är den klassiska distributionsmodellen och är inte längre att föredra.

    b. **Microsoft Azure** är den nya resurshanterarens distributionsmodell.

    c. **Windows Azure drivs av 21Vianet** är ett alternativ i Kina som använder den klassiska distributionsmodellen.

    Om du vill distribuera i Resource Manager-modellen väljer du **Microsoft Azure**.

    ![SAP CAL-kontoinformation](./media/cal-s4h/s4h-pic-2a.png)

1. Ange **Azure-prenumerations-ID** som finns på Azure-portalen.

   ![SAP CAL-konton](./media/cal-s4h/s4h-pic3c.png)

1. Om du vill auktorisera SAP CAL att distribuera till den Azure-prenumeration du definierade klickar du på **Auktorisera**. Följande sida visas på webbläsarfliken:

   ![Logga in i internet explorer-molntjänster](./media/cal-s4h/s4h-pic4c.png)

1. Om mer än en användare visas väljer du det Microsoft-konto som är länkat för att vara medadministrator för den Azure-prenumeration du valde. Följande sida visas på webbläsarfliken:

   ![Bekräftelse av molntjänster i Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

1. Klicka på **Acceptera**. Om auktoriseringen lyckas visas SAP CAL-kontodefinitionen igen. Efter en kort tid bekräftar ett meddelande att auktoriseringsprocessen lyckades.

1. Om du vill tilldela det nyskapade SAP CAL-kontot till användaren anger du ditt **användar-ID** i textrutan till höger och klickar på **Lägg till**.

   ![Konto till användarassociation](./media/cal-s4h/s4h-pic8a.png)

1. Om du vill koppla ditt konto till den användare som du använder för att logga in på SAP CAL klickar du på **Granska**. 
 
1. Om du vill skapa kopplingen mellan användaren och det nyligen skapade SAP CAL-kontot klickar du på **Skapa**.

   ![Användare till SAP CAL-kontoassociation](./media/cal-s4h/s4h-pic9b.png)

Du har skapat ett SAP CAL-konto som kan:

- Använd Resource Manager-distributionsmodellen.
- Distribuera SAP-system till din Azure-prenumeration.

Nu kan du börja distribuera S/4HANA till din användarprenumeration i Azure.

> [!NOTE]
> Innan du fortsätter bestämmer du om du har Azure vCPU-kvoter för virtuella Azure H-serien. För närvarande använder SAP CAL H-serien virtuella datorer i Azure för att distribuera några av de SAP HANA-baserade lösningarna. Din Azure-prenumeration kanske inte har några VCPU-kvoter i H-serien i H-serien. Om så är fallet kan du behöva kontakta Azure-supporten för att få en kvot på minst 16 VCPUs i H-serien.
> 
> [!NOTE]
> När du distribuerar en lösning på Azure i SAP CAL kan du upptäcka att du bara kan välja en Azure-region. Om du vill distribuera till andra Azure-regioner än de som föreslås av SAP CAL måste du köpa en CAL-prenumeration från SAP. Du kan också behöva öppna ett meddelande med SAP för att ditt CAL-konto ska vara aktiverat för att leverera till andra Azure-regioner än de som ursprungligen föreslogs.

### <a name="deploy-a-solution"></a>Distribuera en lösning

Låt oss distribuera en lösning från **sidan Lösningar** på SAP CAL. SAP CAL har två sekvenser att distribuera:

- En grundläggande sekvens som använder en sida för att definiera det system som ska distribueras
- En avancerad sekvens som ger dig vissa alternativ på VM-storlekar 

Vi demonstrerar den grundläggande vägen till distribution här.

1. På sidan **Kontoinformation** måste du:

    a. Välj ett SAP CAL-konto. (Använd ett konto som är associerat för att distribuera med Resurshanterarens distributionsmodell.)

    b. Ange ett **förekomstnamn**.

    c. Välj en **Azure-region**. SAP CAL föreslår en region. Om du behöver en annan Azure-region och du inte har en SAP CAL-prenumeration måste du beställa en CAL-prenumeration med SAP.

    d. Ange ett **huvudlösenord** för lösningen på åtta eller nio tecken. Lösenordet används för administratörer av de olika komponenterna.

   ![SAP CAL Basic Mode: Skapa instans](./media/cal-s4h/s4h-pic10a.png)

1. Klicka på **Skapa**och klicka på **OK**i meddelanderutan som visas.

   ![SAP CAL-stödda VM-storlekar](./media/cal-s4h/s4h-pic10b.png)

1. I dialogrutan **Privat nyckel** klickar du på **Store** för att lagra den privata nyckeln i SAP CAL. Om du vill använda lösenordsskydd för den privata nyckeln klickar du på **Hämta**. 

   ![SAP CAL privat nyckel](./media/cal-s4h/s4h-pic10c.png)

1. Läs **VARNINGSMEDDELANDET** FÖR SAP CAL och klicka på **OK**.

   ![SAP CAL Varning](./media/cal-s4h/s4h-pic10d.png)

    Nu sker distributionen. Efter en tid, beroende på lösningens storlek och komplexitet (SAP CAL ger en uppskattning), visas statusen som aktiv och klar för användning.

1. Om du vill hitta de virtuella datorer som samlas in med de andra associerade resurserna i en resursgrupp går du till Azure-portalen: 

   ![SAP CAL-objekt som distribueras i den nya portalen](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. På SAP CAL-portalen visas statusen som **Aktiv**. Om du vill ansluta till lösningen klickar du på **Anslut**. Olika alternativ för att ansluta till de olika komponenterna distribueras i den här lösningen.

   ![SAP CAL-instanser](./media/cal-s4h/active_solution.png)

1. Innan du kan använda något av alternativen för att ansluta till de distribuerade systemen klickar du på **Komma igång guide**. 

   ![Anslut till instansen](./media/cal-s4h/connect_to_solution.png)

    Dokumentationen namnger användarna för var och en av anslutningsmetoderna. Lösenorden för dessa användare är inställda på huvudlösenordet som du definierade i början av distributionsprocessen. I dokumentationen visas andra mer funktionella användare med sina lösenord, som du kan använda för att logga in på det distribuerade systemet. 

    Om du till exempel använder det SAP-guidning som är förinstallerat på Windows Fjärrskrivbordsdator kan S/4-systemet se ut så här:

   ![SM50 i det förinstallerade SAP GUI](./media/cal-s4h/gui_sm50.png)

    Eller om du använder DBACockpit kan instansen se ut så här:

   ![SM50 i DBACockpit SAP GUI](./media/cal-s4h/dbacockpit.png)

Inom några timmar distribueras en felfri SAP S/4-installation i Azure.

Om du har köpt en SAP CAL-prenumeration stöder SAP distributioner fullt ut via SAP CAL på Azure. Supportkön är BC-VCM-CAL.







