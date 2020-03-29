---
title: Konfigurera och använda offentliga miljöer i Azure DevTest Labs | Microsoft-dokument
description: I den här artikeln beskrivs hur du konfigurerar och använder offentliga miljöer (Azure Resource Manager-mallar i en Git-repo) i Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 127a6986e04cf90f69b2a8ec70b90b877e534708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721701"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Konfigurera och använda offentliga miljöer i Azure DevTest Labs
Azure DevTest Labs har en [offentlig databas med Azure Resource Manager-mallar](https://github.com/Azure/azure-devtestlab/tree/master/Environments) som du kan använda för att skapa miljöer utan att behöva ansluta till en extern GitHub-källa själv. Den här databasen innehåller ofta använda mallar som Azure Web Apps, Service Fabric Cluster och utveckling av SharePoint Farm-miljö. Den här funktionen liknar den offentliga lagringsplatsen för artefakter som ingår för varje labb som du skapar. Med miljölagringsplatsen kan du snabbt komma igång med författade miljömallar med minsta indataparametrar för att ge dig en smidig startupplevelse för PaaS-resurser i labb. 

## <a name="configuring-public-environments"></a>Konfigurera offentliga miljöer
Som labbägare kan du aktivera den offentliga miljön för ditt labb när labbet skapas. Om du vill aktivera offentliga miljöer för ditt labb väljer du **På** för fältet **Offentliga miljöer** när du skapar ett labb. 

![Aktivera offentlig miljö för ett nytt labb](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


För befintliga labb är den offentliga miljön-lagringsplatsen inte aktiverad. Gör det möjligt att använda mallar i databasen manuellt. För övningar som skapats med Hjälp av Resource Manager-mallar inaktiveras databasen som standard också.

Du kan aktivera/inaktivera offentliga miljöer för ditt labb och även göra endast specifika miljöer tillgängliga för labbanvändare med hjälp av följande steg: 

1. Välj **Konfiguration och principer** för ditt labb. 
2. I avsnittet **VIRTUELLA DATORER BASER** väljer du **Offentliga miljöer**.
3. Om du vill aktivera offentliga miljöer för labbet väljer du **Ja**. Annars väljer du **Nej**. 
4. Om du har aktiverat offentliga miljöer aktiveras alla miljöer i databasen som standard. Du kan avmarkera en miljö för att göra den inte tillgänglig för dina labbanvändare. 

![Sida för offentliga miljöer](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Använda miljömallar som labbanvändare
Som labbanvändare kan du skapa en ny miljö från den aktiverade listan över miljömallar genom att helt enkelt välja **+Lägg till** i verktygsfältet på labbsidan. Listan över baser innehåller de offentliga miljömallar som aktiveras av labbadministratören högst upp i listan.

![Mallar för offentlig miljö](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Nästa steg
Den här databasen är en databas med öppen källkod som du kan bidra med för att lägga till vanliga och användbara Resource Manager-mallar. För att bidra skickar du bara en pull-begäran mot databasen.  
