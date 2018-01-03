---
title: Konfigurera SSL-avlastning - Azure Application Gateway - Azure-portalen | Microsoft Docs
description: "Den här artikeln innehåller instruktioner för att skapa en Programgateway med SSL-avlastning med hjälp av Azure portal"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 2f7f5d4132e28c8c192d90d5f4bfb2a9034f8b8c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-azure-portal"></a>Konfigurera en Programgateway för SSL-avlastning med hjälp av Azure portal

> [!div class="op_single_selector"]
> * [Azure-portalen](application-gateway-ssl-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-ssl-arm.md)
> * [Azure klassiska PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway kan konfigureras att avsluta SSL-sessionen (Secure Sockets Layer) på gatewayen så att du undviker kostsamma SSL-dekrypteringsaktiviteter i webbservergruppen. SSL-avlastning förenklar också frontend-serverkonfigurationen och hanteringen av webbappen.

## <a name="scenario"></a>Scenario

Följande scenario tar dig igenom hur du konfigurerar SSL-avlastning på en befintlig Programgateway. Scenariot förutsätter att du redan har följt stegen för att [skapa en Programgateway](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Innan du börjar

Ett certifikat krävs för att konfigurera SSL-avlastning med en Programgateway. Det här certifikatet har lästs in på programgatewayen och används för att kryptera och dekryptera den trafik som skickas via SSL. Certifikatet måste vara i formatet Personal Information Exchange (.pfx). Det här filformatet kan du exportera den privata nyckeln som krävs av programgatewayen att genomföra kryptering och dekryptering av trafik.

## <a name="add-an-https-listener"></a>Lägg till en HTTPS-lyssnare

HTTPS-lyssnaren söker efter trafik baserat på dess konfiguration och hjälper till att vidarebefordra trafiken till backend-pooler. Så här lägger du till en HTTPS-lyssnare:

   1. Bläddra till Azure-portalen och välj en befintlig gateway för programmet.

   2. Välj **lyssnare**, och välj sedan den **Lägg till** för att lägga till en lyssnare.

   ![Programmet Gateway översiktsrutan][1]


   3. Fyll i följande information som behövs för lyssnaren och ladda upp PFX-certifikat:
      - **Namnet**: det egna namnet för lyssnaren.

      - **Frontend-IP-konfiguration**: frontend IP-konfigurationen som används för lyssnaren.

      - **Klientdelsport (namn/Port)**: ett eget namn för den port som används på klientdelen för programgatewayen och den faktiska porten som används.

      - **Protokollet**: en växel för att avgöra om HTTPS eller HTTP används för klientdelen.

      - **Certifikatet (och lösenord)**: avlastning om SSL används, ett PFX-certifikat krävs för den här inställningen. Ett eget namn och lösenord krävs också.

   4. Välj **OK**.

![Lägg till en lyssnare fönstret][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Skapa en regel och koppla den till lyssnaren

Lyssnaren har skapats. Skapa sedan en regel för att hantera trafik från lyssnaren. Används för att definiera hur trafik dirigeras till backend-pooler baserat på flera konfigurationsinställningar. Inställningarna omfattar protokollet, porten och hälsoavsökningar, och om cookie-baserad session tillhörighet ska använda. Följ dessa steg för att skapa och koppla en regel till lyssnaren:


   1. Välj den **regler** Programgateway och välj sedan **Lägg till**.

   ![Rutan för programmet Gateway-regler][3]


   2. Under **Lägg till regel**, ange ett eget namn för regeln i den **namn** och välj sedan den **lyssnare** skapade i föregående steg. Välj lämpliga **serverdelspool** och **HTTP inställningen**, och välj sedan **OK**.

   ![Inställningar för HTTPS][4]

Inställningarna sparas nu i programgatewayen. Spara bearbeta för de här inställningarna kan ta en stund innan de kan visa via portalen eller PowerShell. När den har sparats hanterar programgatewayen kryptering och dekryptering av trafik. All trafik mellan programgatewayen och backend-webb-servrar hanteras via HTTP. All kommunikation tillbaka till klienten, returneras om initieras via HTTPS, till klienten krypteras.

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar en anpassad hälsoavsökningen med Azure Programgateway finns [skapa en anpassad hälsoavsökningen](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

