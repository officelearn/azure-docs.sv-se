---
title: Konfigurera ett SolidWorks-labb för tekniska lösningar med Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb för tekniska kurser med hjälp av SolidWorks.
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: nicolela
ms.openlocfilehash: d4c8a34e9838dc4b6e09f9ed18aa38d16022421f
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450789"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>Konfigurera ett labb för teknik klasser med hjälp av SolidWorks
[SolidWorks](https://www.solidworks.com/) är en 3D-miljö (Computer-Aided Design) för att utforma solida objekt och används i olika teknik fält.  Med SolidWorks kan ingenjörer enkelt skapa, visualisera, simulera och dokumentera sina design.

Ett licensierings alternativ som ofta används av universitet är SolidWorks-nätverks licensiering.   Med det här alternativet delar användare en pool med licenser som hanteras av en licensierings Server.  Den här typen av licens kallas ibland "flytande" licens eftersom du bara behöver ha tillräckligt många licenser för antalet samtidiga användare.  När en användare görs med hjälp av SolidWorks går licensen tillbaka till den centralt hanterade poolen så att den kan återanvändas av en annan användare.

I den här artikeln visar vi hur du konfigurerar en klass som använder SolidWorks 2019 och nätverks licensiering.

## <a name="license-server"></a>Licens Server
SolidWorks-nätverks licensiering kräver att du har SolidNetWork License Manager installerat och aktiverat på licens servern.  Den här licens servern finns vanligt vis antingen i ditt lokala nätverk eller i ett privat nätverk i Azure.  Mer information om hur du konfigurerar SolidNetWork licens hanteraren på servern finns i installera [och aktivera en licens hanterare](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm) i installations guiden för SolidWorks.  När du ställer in detta måste du komma ihåg **port numret** och [**serie numret**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm) som används eftersom de behövs i senare steg.

När licens servern har kon figurer ATS måste du koppla det [virtuella nätverket (VNet)](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) till ditt [labb konto](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account).  Nätverks-peering måste göras innan du skapar labbet så att virtuella labb datorer kan komma åt licens servern och vice versa.

> [!NOTE]
> Kontrol lera att rätt portar är öppna i brand väggarna för att tillåta kommunikation mellan virtuella labb datorer och licens servern.  Se till exempel instruktionerna för att [ändra licens hanterarens dator portar för Windows-brandväggen](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm) som visar hur du lägger till inkommande och utgående regler i licens serverns brand vägg.  Du kan också behöva öppna portar till virtuella labb datorer.  Följ stegen i artikeln om [brand Väggs inställningar för labb](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-firewall-settings) för mer information om detta, inklusive hur du hämtar Labbets offentliga IP-adress.

## <a name="lab-configuration"></a>Labb konfiguration
För att kunna konfigurera det här labbet behöver du ett Azure-prenumerations-och labb konto för att komma igång. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. När du har skaffat en Azure-prenumeration kan du skapa ett nytt labb konto i Azure Lab Services. Mer information om hur du skapar ett nytt labb konto finns i självstudien om [hur du ställer in ett labb konto](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account). Du kan också använda ett befintligt labb konto.

### <a name="lab-account-settings"></a>Labb konto inställningar
Aktivera inställningarna som beskrivs i tabellen nedan för labb kontot. Mer information om hur du aktiverar Marketplace-avbildningar finns i artikeln om [hur du anger Marketplace-avbildningar som är tillgängliga för labb skapare](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Inställning för labb konto | Instruktioner |
| ------------------- | ------------ |
|Marketplace-avbildning| Aktivera Windows 10 Pro-avbildningen för användning i ditt labb konto.|

> [!NOTE]
> Förutom Windows 10 stöder SolidWorks andra versioner av Windows.  Mer information finns i [SolidWorks-system krav](https://www.solidworks.com/sw/support/SystemRequirements.html) .

### <a name="lab-settings"></a>Labb inställningar
Använd inställningarna i tabellen nedan när du konfigurerar ett klass rums labb. Mer information om hur du skapar ett klass rums labb finns i Konfigurera en labb Guide för klass rummet.

| Labb inställningar | Värde/anvisningar |
| ------------ | ------------------ |
|Storlek på virtuell dator| **Liten GPU (visualisering)**.  Den här virtuella datorn passar bäst för fjärrvisualisering, strömning, spel, kodning med hjälp av ramverk som OpenGL och DirectX.|  
|Avbildning av virtuell dator| Windows 10 Pro|

> [!NOTE]
> Storleken på den virtuella datorn med **liten GPU (visualisering)** är konfigurerad för att möjliggöra en grafik upplevelse med hög prestanda.  Mer information om den här virtuella dator storleken finns i artikeln om [hur du konfigurerar ett labb med GPU: er](./how-to-setup-lab-gpu.md).

> [!WARNING]
> Glöm inte att [peer-koppla det virtuella nätverket](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) för labb kontot till det virtuella nätverket för licens servern **innan** du skapar labbet.

## <a name="template-virtual-machine-configuration"></a>Mall för konfiguration av virtuell dator
Stegen i det här avsnittet visar hur du konfigurerar en mall för virtuella datorer genom att ladda ned SolidWorks-installationsfilerna och installera-klient program varan:

1. Starta den virtuella dator mal len och Anslut till datorn via RDP.

1. Hämta installationsfilerna för SolidWorks-klientprogramvaran. Du kan hämta två alternativ:
   - Ladda ned från [SolidWorks-kund Portal](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F).
   - Ladda ned från en katalog på en server.  Om du använder det här alternativet måste du se till att servern är tillgänglig från den virtuella datorns mall.  Den här servern kan till exempel finnas i samma virtuella nätverk som peer-kopplas med ditt labb konto.
  
    Mer information finns i [installation på enskilda datorer i SolidWorks](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0) i SolidWorks installations guide.

1. När installationsfilerna har hämtats installerar du klient programmet med hjälp av SolidWorks installation Manager. Se information om hur du [installerar en licens klient](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm) i SolidWorks installations guide.

    > [!NOTE]
    > I dialog rutan **Lägg till Server** uppmanas du att ange det **port nummer** som används för licens servern och namnet eller IP-adressen för licens servern.

1. När du har installerat klient program varan måste du också aktivera GPU: n som ska användas via RDP-anslutning. Mer information finns i artikeln om [hur du aktiverar GPU över RDP-anslutningar](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms) .

## <a name="cost"></a>Kostnad

Vi ska se en möjlig kostnads uppskattning för den här klassen. Den här beräkningen omfattar inte kostnaden för att köra licens servern. Vi använder en klass av 25 studenter. Det finns 20 timmar med den schemalagda klass tiden. Dessutom får varje student en kvot på 10 timmar för läxor eller tilldelningar utanför schemalagda klass tider. Storleken på den virtuella datorn som vi valde var **liten GPU (visualisering)**, som är 160 lab-enheter.

25 studenter * (20 schemalagda timmar + 10 kvot timmar) * 160 labb enheter * 0,01 USD per timme = 1200,00 USD

>[!IMPORTANT]
> Kostnads uppskattning är till exempel endast syfte.  Aktuell information om priser finns i [Azure Lab Services priser](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att ställa in alla labb.

- [Skapa och hantera en mall](how-to-create-manage-template.md)
- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users)

