---
title: Konfigurera och använda offentliga miljöer i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du konfigurerar och använder offentliga miljöer i Azure DevTest Labs.
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
ms.date: 08/13/2018
ms.author: spelluru
ms.openlocfilehash: d93818cd875c4050b1b35f21ce580933776c5bc5
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235029"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Konfigurera och använda offentliga miljöer i Azure DevTest Labs
Azure DevTest Labs har en [offentliga lagringsplatsen för Azure Resource Manager-mallar](https://github.com/Azure/azure-devtestlab/tree/master/Environments) som du kan använda för att skapa miljöer utan att behöva ansluta till en extern källa för GitHub själv. Den här lagringsplatsen innehåller ofta använda mallar som Azure Web Apps, Service Fabric-kluster och utveckling SharePoint-servergruppen miljö. Den här funktionen liknar den offentliga databasen artefakter som ingår för varje labb som du skapar. Miljö-databasen kan du snabbt komma igång med färdiga miljömallar med minsta indataparametrar för att ge dig en smidig upplevelse för komma igång för PaaS-resurser inom labs. 

## <a name="configuring-public-environments"></a>Konfigurera offentliga miljöer
Som labbägare kan aktivera du offentlig miljö databasen för labbet när du skapar i labbet. Välj för att aktivera offentliga miljöer för dina labb **på** för den **offentliga miljöer** fältet när du skapar ett labb. 

![Aktivera offentlig miljö för ett nytt labb](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


Den offentliga miljö-lagringsplatsen är inte aktiverad för befintliga labb. Aktivera det för att använda mallar i databasen manuellt. Databasen är inaktiverad som standard även för labb som skapats med hjälp av Resource Manager-mallar.

Du kan aktivera/inaktivera offentliga miljöer för dina labb och också tillgängliggör endast specifika miljöer för labbanvändare med hjälp av följande steg: 

1. Välj **konfiguration och principer** för övningen. 
2. I den **VM-BASER** väljer **offentliga miljöer**.
3. Om du vill aktivera offentliga miljöer för labbet, Välj **Ja**. Annars väljer **nr**. 
4. Om du har aktiverat offentliga miljöer är alla miljöer i databasen aktiverade som standard. Du kan avmarkera en miljö för att göra den inte tillgänglig för dina labbanvändare. 

![Sidan i offentliga miljöer](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Använda miljömallar som en lab-användare
Som en lab-användare kan du skapa en ny miljö från listan över aktiverade av miljömallar genom att helt enkelt välja **+ Lägg till** från verktygsfältet på sidan labb. Listan över databaser innehåller de offentliga miljöer mallar som aktiverats av administratören lab överst i listan.

![Offentliga miljömallar](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Nästa steg
Den här databasen är en lagringsplats för öppen källkod som du kan bidra med för att lägga till ofta använda och hjälpsam Resource Manager-mallar på egen hand. För att bidra, bara skicka en pull-begäran mot databasen.  
