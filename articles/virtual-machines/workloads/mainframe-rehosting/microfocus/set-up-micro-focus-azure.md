---
title: Installera Micro Focus Enterprise Server 4.0 och Enterprise Developer 4.0 på Azure | Microsoft-dokument
description: Rehost din IBM z / OS stordator arbetsbelastningar med hjälp av Micro Focus utvecklings-och testmiljö på Azure virtuella datorer (VIRTUELLA datorer).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 03/30/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 5b3fa5faccf17df17bc4f7cc2d8b023b868fdbe1
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411199"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Installera Micro Focus Enterprise Server 4.0 och Enterprise Developer 4.0 på Azure

Den här artikeln visar hur du konfigurerar [Micro Focus Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) och Micro Focus Enterprise Developer [4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) på Azure.

En gemensam arbetsbelastning på Azure är en utvecklings- och testmiljö. Det här scenariot är vanligt eftersom det är så kostnadseffektivt och enkelt att distribuera och riva. Med Enterprise Server har Micro Focus skapat en av de största plattformarna för rehosting i stordatorn. Du kan köra z/OS-arbetsbelastningar på en billigare x86-plattform på Azure med antingen Windows eller Linux virtuella datorer (VIRTUELLA datorer).

> [!NOTE]
> Kommer snart: Instruktioner för hur du konfigurerar [Micro Focus Enterprise Server 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) på virtuella Azure-datorer.

Den här konfigurationen använder virtuella Azure-datorer som kör Windows Server 2016-avbildningen från Azure Marketplace med Microsoft SQL Server 2017 redan installerat. Den här inställningen gäller även för Azure Stack.

Motsvarande utvecklingsmiljö för Enterprise Server är Enterprise Developer, som körs på antingen Microsoft Visual Studio 2017 eller senare, Visual Studio Community (gratis att ladda ned) eller Eclipse. Den här artikeln visar hur du distribuerar den med en virtuell Windows Server 2016-dator som medföljer Visual Studio 2017 eller senare installerad.

## <a name="prerequisites"></a>Krav

Innan du börjar kan du läsa in följande förutsättningar:

- En Azure-prenumeration. Om du inte har ett, skapa ett [gratis konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Micro Focus-programvaran och en giltig licens (eller utvärderingslicens). Om du är en befintlig Micro Focus-kund kontaktar du din Micro Focus-representant. Annars [begär du en rättegång](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Hämta dokumentationen för [Enterprise Server och Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> En bra metod är att konfigurera en VPN-tunnel (Site to-site Virtual Private Network) eller en jumpbox så att du kan styra åtkomsten till virtuella Azure-datorer.

## <a name="install-enterprise-server"></a>Installera Enterprise Server

1. För bättre säkerhet och hanterbarhet bör du överväga att skapa en ny resursgrupp bara för det här projektet, till exempel **RGMicroFocusEntServer**. Använd den första delen av namnet i Azure för att välja vilken typ av resurs som gör det enklare att plats i en lista.

2. Skapa en virtuell dator. Välj den virtuella dator och det operativsystem du vill använda på Azure Marketplace. Här är en rekommenderad inställning:

    - **Enterprise Server**: Välj ES2 v3 VM (med 2 vCPUs och 16 GB minne) med Windows Server 2016 och SQL Server 2017 installerade. Den här avbildningen är tillgänglig från Azure Marketplace. Enterprise Server kan också använda Azure SQL Database.

    - **Enterprise Developer**: Välj B2ms VM (med 2 vCPUs och 8 GB minne) med Windows 10 och Visual Studio installerat. Den här avbildningen är tillgänglig från Azure Marketplace.

3. I avsnittet **Grunderna** anger du ditt användarnamn och lösenord. Välj den **prenumeration** och **plats/region** som du vill använda för de virtuella datorerna. Välj **RGMicroFocusEntServer** för resursgruppen.

4. Placera båda virtuella datorer i samma virtuella nätverk så att de kan kommunicera med varandra.

5. Acceptera standardinställningarna för resten av inställningarna. Kom ihåg användarnamnet och lösenordet som du skapar för administratören av dessa virtuella datorer.

6. När de virtuella datorerna har skapats öppnar du inkommande portar 9003, 86 och 80 för HTTP och 3389 för RDP på Enterprise Server-datorn och 3389 på utvecklardatorn.

7. Om du vill logga in på den virtuella enterprise server-datorn i Azure-portalen väljer du den virtuella virtuella datorn ES2 v3. Gå till avsnittet **Översikt** och välj **Anslut** för att starta en RDP-session. Logga in med de autentiseringsuppgifter som du skapade för den virtuella datorn.

8. Läs in följande två filer från RDP-sessionen. Eftersom du använder Windows kan du dra och släppa filerna i RDP-sessionen:

    - **es\_40.exe**, installationsfilen för Enterprise Server.

    - **mflic**, motsvarande licensfil – Enterprise Server läses inte in utan den.

9. Dubbelklicka på filen för att starta installationen. I det första fönstret väljer du installationsplatsen och accepterar licensavtalet för slutanvändare.

     ![Inställningsskärmen för Micro Focus Enterprise Server](media/01-enterprise-server.png)

     När installationen är klar visas följande meddelande:

     ![Inställningsskärmen för Micro Focus Enterprise Server](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Sök efter uppdateringar

Efter installationen måste du söka efter ytterligare uppdateringar eftersom ett antal förutsättningar, till exempel Microsoft C++ Redistributable och .NET Framework, installeras tillsammans med Enterprise Server.

### <a name="upload-the-license"></a>Ladda upp licensen

1. Starta Micro Focus License Administration.

2. Klicka på **Starta** \> **Licensadministration** \> för Micro Focus **License Manager**och klicka sedan på fliken **Installera.** Välj vilken typ av licensformat som ska laddas upp: en licensfil eller en licenskod på 16 tecken. Till exempel för en fil, i **licensfil**, bläddra till **mflic-filen** som tidigare överförts till den virtuella datorn och välj **Installera licenser**.

     ![Dialogrutan Administration av micro focus-licens](media/03-enterprise-server.png)

3. Kontrollera att Enterprise Server läses in. Prova att starta webbplatsen För administration av <http://localhost:86/> Enterprise Server från en webbläsare med den här URL:en . Sidan Administration av Företagsserver visas som visas.

     ![Sidan Administration av Företags server](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Installera Enterprise Developer på utvecklardatorn

1. Välj den resursgrupp som skapats tidigare (till exempel **RGMicroFocusEntServer**) och välj sedan utvecklaravbildningen.

2. Om du vill logga in på den virtuella datorn går du till avsnittet **Översikt** och väljer **Anslut**. Den här inloggningen startar en RDP-session. Logga in med de autentiseringsuppgifter som du skapade för den virtuella datorn.

3. Från RDP-sessionen läser du in följande två filer (dra och släpp om du vill):

    - **edvs2017.exe**, installationsfilen för Enterprise Server.

    - **mflic**, motsvarande licensfil (Enterprise Developer laddas inte utan den).

4. Dubbelklicka på filen **edvs2017.exe** för att starta installationen. I det första fönstret väljer du installationsplatsen och accepterar licensavtalet för slutanvändare. Om du vill kan du välja **Installera Rumba 9.5** för att installera den här terminalemulatorn, som du förmodligen behöver.

     ![Dialogrutan Installationsprogram för Micro Focus Enterprise-utvecklare för Visual Studio 2017](media/04-enterprise-server.png)

5. När installationen är klar visas följande meddelande:

     ![Meddelande om installationsprogrammet har slutförts](media/05-enterprise-server.png)

6. Starta Licenshanteraren för Micro Focus precis som för Enterprise Server. Välj **Starta** \> **Licensadministration** \> för Micro Focus **License Manager**och klicka på fliken **Installera.**

7. Välj vilken typ av licensformat som ska laddas upp: en licensfil eller en licenskod på 16 tecken. Till exempel för en fil, i **licensfil**, bläddra till **mflic-filen** som tidigare överförts till den virtuella datorn och välj **Installera licenser**.

     ![Dialogrutan Administration av micro focus-licens](media/07-enterprise-server.png)

När Enterprise Developer läses in är distributionen av en utvecklings- och testmiljö för Micro Focus på Azure klar!

## <a name="next-steps"></a>Nästa steg

- [Konfigurera bankdemonstrationsprogrammet](./demo.md)
- [Kör Enterprise Server i Docker-behållare](./run-enterprise-server-container.md)
- [Migrering av program från stordatorer](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
