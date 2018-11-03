---
title: Hantera roller i Azure cloud services med Visual Studio | Microsoft Docs
description: Lär dig hur du lägger till och ta bort roller i Azure cloud services med Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 5ec9ae2e-8579-4e5d-999e-8ae05b629bd1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 835494f169b29dc746e4acaad8a3155fd63948a8
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969260"
---
# <a name="managing-roles-in-azure-cloud-services-with-visual-studio"></a>Hantera roller i Azure cloud services med Visual Studio
När du har skapat din Azure-molntjänst, kan du lägga till nya roller eller ta bort befintliga roller från den. Du kan också importera ett befintligt projekt och konvertera den till en roll. Du kan till exempel importera ASP.NET-webbprogram och ange den som en webbroll.

## <a name="adding-a-role-to-an-azure-cloud-service"></a>Att lägga till en roll till en Azure-molntjänst
Följande steg beskriver hur du lägger till en web- eller worker-roll till en Azure cloud service-projekt i Visual Studio.

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, expandera projektnoden

1. Högerklicka på den **roller** noder för att visa snabbmenyn. På snabbmenyn väljer **Lägg till**, Välj en befintlig webbroll eller worker-rollen från den aktuella lösningen eller skapa ett projekt för webb- eller worker-rollen. Du kan också välja ett lämpligt projekt, till exempel ett ASP.NET webb-programprojekt, och koppla den till en rollprojekt.

    ![Menyn Alternativ för att lägga till en roll till en Azure cloud service-projekt](media/vs-azure-tools-cloud-service-project-managing-roles/add-role.png)

## <a name="removing-a-role-from-an-azure-cloud-service"></a>Ta bort en roll från en Azure-molntjänst
Följande steg beskriver hur du tar bort en web- eller worker-roll från ett Azure cloud service-projekt i Visual Studio.

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, expandera projektnoden

1. Expandera den **roller** noden.

1. Högerklicka på noden som du vill ta bort och välj sedan från snabbmenyn **ta bort**. 

    ![Menyn Alternativ för att lägga till en roll till en Azure-molntjänst](media/vs-azure-tools-cloud-service-project-managing-roles/remove-role.png)

## <a name="readding-a-role-to-an-azure-cloud-service-project"></a>Lägg till igen en roll till en Azure cloud service-projekt
Om du tar bort en roll från ditt molntjänstprojekt men senare vill lägga till rollen i projektet, läggs endast rollen deklarationen och grundläggande attribut, till exempel slutpunkter och diagnostik. Inga ytterligare resurser eller referenser som har lagts till i `ServiceDefinition.csdef` fil eller till den `ServiceConfiguration.cscfg` filen. Om du vill lägga till den här informationen kan behöva du manuellt lägga tillbaka det i de här filerna.

Exempel: du kan ta bort en webbtjänstrollen och du senare vill lägga till den här rollen tillbaka till din lösning. Om du gör det uppstår ett fel. Om du vill förhindra att det här felet, måste du lägga till den `<LocalResources>` element som visas i följande XML tillbaka till den `ServiceDefinition.csdef` filen. Använd namnet på webbtjänstrollen som du lagt till tillbaka till projektet som en del av namnattributet för den **<LocalStorage>** element. I det här exemplet är namnet på webbtjänstrollen **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Nästa steg
- [Konfigurera roller för en Azure-molntjänst med Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md)
