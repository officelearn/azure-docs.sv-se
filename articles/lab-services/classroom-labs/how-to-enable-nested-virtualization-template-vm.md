---
title: Aktivera kapslad virtualisering på en virtuell mall-dator i Azure Lab Services | Microsoft-dokument
description: Lär dig hur du skapar en virtuell mall med flera virtuella datorer inuti.  Med andra ord aktivera kapslad virtualisering på en virtuell mall-dator i Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 59b32834369f76d39bb4a253dad4ec541e7ef999
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502003"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Aktivera kapslad virtualisering på en virtuell malldator i Azure Lab Services

För närvarande kan Azure Lab Services du ställa in en mall virtuell dator i ett labb och göra en enda kopia tillgänglig för var och en av dina användare. Om du undervisar i nätverk, säkerhet eller IT-klasser kan du behöva ge var och en av dina elever en miljö där flera virtuella datorer kan prata med varandra via ett nätverk.

Kapslad virtualisering gör att du kan skapa en miljö med flera virtuella datorer i ett labbs virtuella malldator. Publicera mallen kommer att ge varje användare i labbet med en virtuell dator som inrättats med flera virtuella datorer i den.  Den här artikeln beskriver hur du konfigurerar kapslad virtualisering på en malldator i Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Vad är kapslad virtualisering?

Kapslad virtualisering gör att du kan skapa virtuella datorer på en virtuell dator. Kapslad virtualisering sker via Hyper-V och är endast tillgängligt på virtuella datorer i Windows.

Mer information om kapslad virtualisering finns i följande artiklar:

- [Kapslad virtualisering i Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Aktivera kapslad virtualisering i en virtuell Azure-dator](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Överväganden

Innan du ställer in ett labb med kapslad virtualisering, här är några saker att ta hänsyn till.

- När du skapar ett nytt labb väljer du **Medelstora (kapslade virtualiserings-** eller **stora (kapslade virtualiseringsstorlekar)** för den virtuella datorns storlek. Dessa storlekar för virtuella datorer stöder kapslad virtualisering.
- Välj en storlek som ger bra prestanda för både värd- och klient virtuella datorer.  Kom ihåg att när du använder virtualisering måste den storlek du väljer vara tillräcklig för inte bara en dator, men värden samt alla klientdatorer som måste köras samtidigt.
- Klient virtuella datorer kommer inte att ha åtkomst till Azure-resurser, till exempel DNS-servrar i det virtuella Azure-nätverket.
- Värd virtuell dator kräver installation för att klientdatorn ska ha internetanslutning.
- Klient virtuella datorer är licensierade som oberoende datorer. Mer information om licensiering av Microsofts operativsystem och produkter finns i [Microsofts](https://www.microsoft.com/licensing/default) licensieringstillstånd. Kontrollera licensavtal för andra program som används innan du ställer in mallen maskinen.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Aktivera kapslad virtualisering på en virtuell malldator

Den här artikeln förutsätter att du har skapat ett labbkonto och labb.  Mer information om hur du skapar ett nytt labbkonto finns i [självstudiekursen för att konfigurera ett labbkonto](tutorial-setup-lab-account.md). Mer information om hur du skapar labb finns i [konfigurera en självstudiekurs för klassrumslabb](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Välj **Stor (kapslad virtualisering)** eller **Medel (kapslad virtualisering)** för den virtuella datorns storlek när du skapar labbet.  Kapslad virtualisering fungerar inte annars.  

Information om hur du ansluter till mallens dator finns i [skapa och hantera en klassrumsmall](how-to-create-manage-template.md).

### <a name="using-script-to-enable-nested-virtualization"></a>Använda skript för att aktivera kapslad virtualisering

Information om hur du använder den automatiska installationen för kapslad virtualisering med Windows Server 2016 eller Windows Server 2019 finns [i Aktivera kapslad virtualisering på en virtuell malldator i Azure Lab Services med ett skript](how-to-enable-nested-virtualization-template-vm-using-script.md). Du kommer att använda skript från [Lab Services Hyper-V-skript](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) för att installera Hyper-V-rollen.  Skripten kommer också att konfigurera nätverk så att Hyper-V virtuella datorer kan ha tillgång till Internet.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Använda Windows-verktyg för att aktivera kapslad virtualisering

Den kapslade virtualiseringen av Windows Server 2016 eller Windows Server 2019 med Windows-roller och administrationsverktyg finns [i Aktivera kapslad virtualisering på en virtuell malldator i Azure Lab Services manuellt](how-to-enable-nested-virtualization-template-vm-ui.md).  Instruktioner kommer också att omfatta hur du ställer in nätverk så att Hyper-V virtuella datorer kan ha tillgång till Internet.
