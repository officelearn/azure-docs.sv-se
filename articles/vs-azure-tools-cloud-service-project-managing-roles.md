---
title: Hantera roller i Azure-molntjänster med Visual Studio | Microsoft Docs
description: Lär dig mer om att lägga till och ta bort roller i Azure-molntjänster med Visual Studio.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 5ec9ae2e-8579-4e5d-999e-8ae05b629bd1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: fb49eaa69f2265f6b312ecbdab1435e7c50236b8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="managing-roles-in-azure-cloud-services-with-visual-studio"></a>Hantera roller i Azure-molntjänster med Visual Studio
När du har skapat din Azure-molntjänst kan du lägga till nya roller eller ta bort befintliga roller från den. Du kan också importera ett befintligt projekt och konvertera det till en roll. Du kan till exempel importera ASP.NET-webbprogram och ange det som en webbroll.

## <a name="adding-a-role-to-an-azure-cloud-service"></a>Att lägga till en roll till en Azure-molntjänst
Följande steg hur du lägger till rollen webb eller arbetare i ett Azure cloud service-projekt i Visual Studio.

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, expandera projektnoden

1. Högerklicka på den **roller** noder för att visa snabbmenyn. På snabbmenyn Välj **Lägg till**, Välj en befintlig webbroll eller worker-rollen från den aktuella lösningen eller skapa ett projekt för webb- eller worker-rollen. Du kan också Välj en lämplig, till exempel ett ASP.NET web application-projekt och associera det med ett rollprojekt.

    ![Menyn Alternativ för att lägga till en roll till en Azure cloud service-projekt](media/vs-azure-tools-cloud-service-project-managing-roles/add-role.png)

## <a name="removing-a-role-from-an-azure-cloud-service"></a>Ta bort en roll från en Azure-molntjänst
Följande steg hur du tar bort en webbplats eller arbetsprocess roll från ett Azure cloud service-projekt i Visual Studio.

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, expandera projektnoden

1. Expandera den **roller** nod.

1. Högerklicka på den nod som du vill ta bort och på snabbmenyn Välj **ta bort**. 

    ![Menyn Alternativ för att lägga till en roll till en Azure-molntjänst](media/vs-azure-tools-cloud-service-project-managing-roles/remove-role.png)

## <a name="readding-a-role-to-an-azure-cloud-service-project"></a>Readding en roll till en Azure cloud service-projekt
Om du tar bort en roll från ditt molntjänstprojekt men senare bestämmer dig för att lägga till rollen i projektet, läggs endast rollen deklaration och grundläggande attribut, till exempel information om slutpunkter och diagnostik. Inga ytterligare resurser eller referenser som har lagts till i den `ServiceDefinition.csdef` filen eller den `ServiceConfiguration.cscfg` filen. Om du vill lägga till den här informationen måste du manuellt lägga tillbaka det i dessa filer.

Till exempel kan du ta bort en webbtjänstrollen och du senare vill lägga till den här rollen i din lösning igen. Om du gör det uppstår ett fel. För att förhindra det här felet måste du lägga till den `<LocalResources>` element som visas i följande XML-filen till den `ServiceDefinition.csdef` filen. Använd namnet på rollen webbserver som du har lagt till projektet som en del av namnattributet för den **<LocalStorage>** element. I det här exemplet är namnet på webbtjänstrollen **WCFServiceWebRole1**.

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
