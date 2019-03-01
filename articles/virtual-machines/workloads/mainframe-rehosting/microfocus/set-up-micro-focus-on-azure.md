---
title: Installera Micro fokus Enterprise Server 4.0 och företagsutvecklare 4.0 på Azure | Microsoft Docs
description: Ange ny värd för dina IBM z/OS stordatorprogram arbetsbelastningar med Micro fokus utvecklingen och testmiljö i Azure virtual machines (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 46b135aabaefb3a94e4470927297fb696f216b7a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192538"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Installera Micro fokus Enterprise Server 4.0 och företagsutvecklare 4.0 på Azure

Den här artikeln visar hur du ställer in [Micro fokus Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) och [Micro fokus Enterprise Developer 4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) på Azure.

En gemensam arbetsbelastning i Azure är en miljö för utveckling och testning, eftersom det är så kostnadseffektiv och enkel att distribuera och plocka ner. Med Enterprise-Server, har Micro fokus skapat en av de största stordatorprogram rehosting plattformarna tillgängliga. Du kan köra z/OS-arbetsbelastningar på en billigare x86 plattform på Azure med hjälp av antingen Windows eller Linux-datorer (VM).

Den här konfigurationen använder virtuella Azure-datorer som kör Windows Server 2016-avbildning från Azure Marketplace med Microsoft SQL Server 2017 är redan installerad. Den här inställningen gäller även för Azure Stack.

Motsvarande utvecklingsmiljö för Enterprise Server är företagsutvecklare som körs på antingen Microsoft Visual Studio 2017, Visual Studio Community (gratis att ladda ned), eller Eclipse. Den här artikeln visar hur du distribuerar den med hjälp av en virtuell Windows Server 2016-dator som ingår i Visual Studio 2017 installerat.

## <a name="prerequisites"></a>Förutsättningar

Kolla in dessa krav innan du börjar:

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Programvaran Micro fokus och en giltig licens (eller utvärderingslicens). Om du är en befintlig Micro fokus-kund kan du kontakta din Micro fokus-representant. I annat fall [begär en utvärderingsversion](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Hämta dokumentationen för [Enterprise Server och företagsutvecklare](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> Ett bra tips är att konfigurera en tunnel för plats-till-plats virtuellt privat nätverk (VPN) eller en jumpbox så att du kan styra åtkomsten till virtuella Azure-datorer.

## <a name="install-enterprise-server"></a>Installera Enterprise Server

1. Överväg att skapa en ny resursgrupp för det här projektet för bättre säkerhet och hanterbarhet – till exempel **RGMicroFocusEntServer**. Använd den första delen av namnet i Azure för att ange vilken typ av resurs att göra det enklare att hitta i en lista.

2. Skapa en virtuell dator. Azure Marketplace, Välj den virtuella datorn och operativsystemet som du vill. Här är en rekommenderad konfiguration:

    - **Enterprise Server**: Välj ES2 v3 VM (med 2 virtuella processorer och 16 GB minne) med Windows Server 2016 och SQL Server 2017 installerat. Den här bilden är tillgängliga från Azure Marketplace. Enterprise Server kan också använda Azure SQL Database.

    - **Företagsutvecklare**: Välj B2ms virtuell dator (med 2 virtuella processorer och 8 GB minne) med Windows 10 och Visual Studio installerat. Den här bilden är tillgängliga från Azure Marketplace.

3. På den **grunderna** bladet anger du användarnamn och lösenord. Välj den **prenumeration** och **plats/Region** som ska användas för de virtuella datorerna. Välj **RGMicroFocusEntServer** för resursgruppen.

4. Placera båda virtuella datorerna i samma virtuella nätverk så att de kan kommunicera med varandra.

5. Acceptera standardinställningarna för resten av inställningarna. Kom ihåg att användarnamnet och lösenordet som du skapar för administratören för dessa virtuella datorer.

6. När de virtuella datorerna har skapats, öppna ingående portar 9003, 86 och 80 för HTTP och 3389 för RDP på Enterprise Server-datorn och 3389 på Developer-datorn.

7. Välj den virtuella datorn v3 ES2 för att logga in till Enterprise Server-dator, Azure-portalen. Gå till den **översikt** bladet och välj **Connect** att starta en RDP-session. Logga in med de autentiseringsuppgifter som du skapade för den virtuella datorn.

8. Läsa in följande två filer från RDP-session. Eftersom detta är Windows, så du kan dra och släppa filer till RDP-session:

    - **ES\_40. exe**, Enterprise Server-installationsfilen.

    - **mflic**, motsvarande licensfilen – Enterprise Server kommer inte att läsa in utan den.

9. Dubbelklicka på filen för att starta installationen. I det första fönstret Välj installationsplats och acceptera licensavtalet för slutanvändare.

     ![Micro fokus Enterprise Server-konfiguration av företagsåtkomst](media/01-enterprise-server.png)

     När installationen är klar visas följande meddelande:

     ![Micro fokus Enterprise Server-konfiguration av företagsåtkomst](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Sök efter uppdateringar

Efter installationen, måste du söka efter eventuella ytterligare uppdateringar sedan ett antal förutsättningar som Microsoft C++ Redistributable och .NET Framework installeras tillsammans med Enterprise Server.

### <a name="upload-the-license"></a>Ladda upp licensen

1. Starta Micro fokus licens Administration.

2. Klicka på **starta** \> **Micro fokus License Manager** \> **licens Administration**, och klicka sedan på den **installera** fliken. Välj typ av licens-format för att ladda upp: en licensfil eller en licenskod på 16 tecken. Till exempel för en fil i **licensfil**, bläddra till den **mflic** tidigare överföra filen till den virtuella datorn och välj **installera licenser**.

     ![Dialogrutan för Micro fokus licens Administration](media/03-enterprise-server.png)

3. Kontrollera att Enterprise Server läses in. Försök starta den Enterprise Server-administrationsplatsen från en webbläsare med den här URL: en <http://localhost:86/> . Sidan Enterprise Server Administration visas som visas.

     ![Enterprise Server administrationssidan](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Installera företagsutvecklare på developer-dator

1. Välj den resursgrupp som du skapade tidigare (till exempel **RGMicroFocusEntServer**), Välj developer-bild.

2. Om du vill logga in på den virtuella datorn, gå till den **översikt** bladet och välj **Connect**. Detta startar en RDP-session. Logga in med de autentiseringsuppgifter som du skapade för den virtuella datorn.

3. Läs in följande två filer (dra och släpp om du vill) från RDP-session:

    - **edvs2017.exe**, Enterprise Server-installationsfilen.

    - **mflic**, motsvarande licensfilen (företagsutvecklare inte kommer att läsa in utan det).

4. Dubbelklicka på den **edvs2017.exe** filen för att starta installationen. I det första fönstret Välj installationsplats och acceptera licensavtalet för slutanvändare. Om du vill välja **installera surfa 9.5** att installera den här terminalemulator som du behöver förmodligen.

     ![Micro fokus företagsutvecklare för dialogrutan Installationsprogram för Visual Studio 2017](media/04-enterprise-server.png)

5. När installationen är klar visas följande meddelande:

     ![Installationen lyckades meddelande](media/05-enterprise-server.png)

6. Starta Micro fokus License Manager precis som du gjorde för Enterprise-Server. Välj **starta** \> **Micro fokus License Manager** \> **licens Administration**, och klicka på den **installera**fliken.

7. Välj typ av licens-format för att ladda upp: en licensfil eller en licenskod på 16 tecken. Till exempel för en fil i **licensfil**, bläddra till den **mflic** tidigare överföra filen till den virtuella datorn och välj **installera licenser**.

     ![Dialogrutan för Micro fokus licens Administration](/edia/07-enterprise-server.png)

När företagsutvecklare har lästs in är din miljö för utveckling och test en Micro fokus på Azure-distribution klar!

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Bank demoprogrammet](./demo.md)
