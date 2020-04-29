---
title: Installera Micro Focus Enterprise Server 4,0 och Enterprise Developer 4,0 på Azure | Microsoft Docs
description: Revara värd för dina IBM z/OS-arbetsbelastningar med hjälp av Micro Focus-utveckling och test miljö på Azure Virtual Machines (VM).
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80411199"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Installera Micro Focus Enterprise Server 4,0 och Enterprise Developer 4,0 på Azure

Den här artikeln visar hur du konfigurerar [Micro Focus Enterprise Server 4,0](https://www.microfocus.com/documentation/enterprise-developer/es30/) och [Micro Focus enterprise Developer 4,0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) på Azure.

En gemensam arbets belastning i Azure är en utvecklings-och test miljö. Det här scenariot är vanligt eftersom det är så kostnads effektivt och enkelt att distribuera och avbryta. Med Enterprise Server har Micro Focus skapat en av de största tillgängliga plattformarna för stordatorer som är tillgängliga. Du kan köra z/OS-arbetsbelastningar på en billigare x86-plattform på Azure med antingen virtuella Windows-eller Linux-datorer (VM).

> [!NOTE]
> Kommer snart: anvisningar för att konfigurera [Micro Focus Enterprise Server 5,0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) på virtuella Azure-datorer.

Den här installationen använder virtuella Azure-datorer som kör Windows Server 2016-avbildningen från Azure Marketplace med Microsoft SQL Server 2017 redan har installerats. Den här inställningen gäller även för Azure Stack.

Motsvarande utvecklings miljö för Enterprise Server är företags utvecklare, som körs på antingen Microsoft Visual Studio 2017 eller senare, Visual Studio Community (kostnads fri att ladda ned) eller Sol förmörkelse. Den här artikeln visar hur du distribuerar den med en virtuell Windows Server 2016-dator som medföljer Visual Studio 2017 eller senare installerat.

## <a name="prerequisites"></a>Krav

Innan du börjar bör du gå igenom följande krav:

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Micro Focus-programvaran och en giltig licens (eller prov licens). Om du är en befintlig Micro Focus-kund kontaktar du din Micro Focus-representant. Annars kan du [begära en utvärderings version](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Hämta dokumentationen för [Enterprise Server och Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> Ett bra tips är att konfigurera en plats-till-plats-tunnel för virtuella privata nätverk (VPN) eller en hopp ruta så att du kan styra åtkomsten till de virtuella Azure-datorerna.

## <a name="install-enterprise-server"></a>Installera Enterprise Server

1. Överväg att skapa en ny resurs grupp för det här projektet, till exempel **RGMicroFocusEntServer**, för bättre säkerhet och hanterbarhet. Använd den första delen av namnet i Azure om du vill välja typ av resurs för att göra det lättare att hitta dem i en lista.

2. Skapa en virtuell dator. Från Azure Marketplace väljer du den virtuella dator och det operativ system som du vill använda. Här är en rekommenderad installation:

    - **Enterprise Server**: Välj ES2 v3 VM (med 2 virtuella processorer och 16 GB minne) med Windows Server 2016 och SQL Server 2017 installerat. Den här avbildningen är tillgänglig från Azure Marketplace. Företags servern kan även använda Azure SQL Database.

    - **Enterprise-utvecklare**: Välj B2ms VM (med 2 virtuella processorer och 8 GB minne) med Windows 10 och Visual Studio installerat. Den här avbildningen är tillgänglig från Azure Marketplace.

3. I avsnittet **grundläggande** anger du ditt användar namn och lösen ord. Välj den **prenumeration** och **plats/region** som du vill använda för de virtuella datorerna. Välj **RGMicroFocusEntServer** för resurs gruppen.

4. Sätt båda virtuella datorerna i samma virtuella nätverk så att de kan kommunicera med varandra.

5. Acceptera standardinställningarna för resten av inställningarna. Kom ihåg det användar namn och lösen ord som du skapar för administratören för dessa virtuella datorer.

6. När de virtuella datorerna har skapats öppnar du de inkommande portarna 9003, 86 och 80 för HTTP och 3389 för RDP på Enterprise Server-datorn och 3389 på Developer-datorn.

7. Om du vill logga in på den virtuella Enterprise Server-datorn väljer du den virtuella ES2 v3-datorn i Azure Portal. Gå till avsnittet **Översikt** och välj **Anslut** för att starta en RDP-session. Logga in med de autentiseringsuppgifter som du skapade för den virtuella datorn.

8. Läs in följande två filer från RDP-sessionen. Eftersom du använder Windows kan du dra och släppa filerna i RDP-sessionen:

    - **es\_40. exe**, installations filen för företags servern.

    - **mflic**, motsvarande licens fil – Enterprise Server läser inte in utan den.

9. Starta installationen genom att dubbelklicka på filen. I det första fönstret väljer du installations platsen och accepterar slut användar avtalet.

     ![Installations skärmen för Micro Focus Enterprise Server](media/01-enterprise-server.png)

     När installationen är klar visas följande meddelande:

     ![Installations skärmen för Micro Focus Enterprise Server](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Sök efter uppdateringar

Efter installationen måste du kontrol lera om det finns fler uppdateringar, till exempel Microsoft C++ Redistributable och .NET Framework installeras tillsammans med Enterprise Server.

### <a name="upload-the-license"></a>Ladda upp licensen

1. Starta licens administration för Micro Focus.

2. Klicka på **Start** \> **Micro Focus License Manager** \> **License administration**och klicka sedan på fliken **Installera** . Välj vilken typ av licens format som ska överföras: en licens fil eller en licens kod på 16 tecken. Till exempel, i **licens fil**, bläddrar du till **mflic** -filen som laddades upp tidigare till den virtuella datorn och väljer **Installera licenser**.

     ![Dialog rutan licens administration för Micro Focus](media/03-enterprise-server.png)

3. Kontrol lera att Enterprise Server läser in. Försök att starta företags serverns administrations webbplats från en webbläsare som <http://localhost:86/> använder den här URL: en. Sidan administration av företags server visas som visas.

     ![Sidan administration av företags server](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Installera Enterprise Developer på Developer Machine

1. Välj den resurs grupp som skapades tidigare (till exempel **RGMicroFocusEntServer**) och välj sedan Developer-avbildningen.

2. Om du vill logga in på den virtuella datorn går du till avsnittet **Översikt** och väljer **Anslut**. Den här inloggningen startar en RDP-session. Logga in med de autentiseringsuppgifter som du skapade för den virtuella datorn.

3. Läs in följande två filer från RDP-sessionen (dra och släpp om du vill):

    - **edvs2017. exe**, installations filen för företags servern.

    - **mflic**, motsvarande licens fil (Enterprise Developer kommer inte att läsas in utan den).

4. Starta installationen genom att dubbelklicka på filen **edvs2017. exe** . I det första fönstret väljer du installations platsen och accepterar slut användar avtalet. Om du vill kan du välja **Installera Rumba 9,5** för att installera denna termin Ale mula Tor som du förmodligen behöver.

     ![Konfigurations dialog rutan för Micro Focus Enterprise Developer för Visual Studio 2017](media/04-enterprise-server.png)

5. När installationen är klar visas följande meddelande:

     ![Installations meddelandet har slutförts](media/05-enterprise-server.png)

6. Starta Micro Focus License Manager precis som du gjorde för företags servern. Välj **Starta** \> **Micro Focus License Manager** \> **License administration**och klicka på fliken **Installera** .

7. Välj typ av licens format som ska överföras: en licens fil eller en licens kod på 16 tecken. Till exempel, i **licens fil**, bläddrar du till **mflic** -filen som laddades upp tidigare till den virtuella datorn och väljer **Installera licenser**.

     ![Dialog rutan licens administration för Micro Focus](media/07-enterprise-server.png)

När Enterprise Developer laddas, är din distribution av en Micro Focus-utveckling och test miljö på Azure slutförd!

## <a name="next-steps"></a>Nästa steg

- [Konfigurera bank demonstrations programmet](./demo.md)
- [Kör Enterprise Server i Docker-behållare](./run-enterprise-server-container.md)
- [Migrering av program från stordatorer](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
