---
title: Installera en lokal datagateway | Microsoft Docs
description: Lär dig hur du installerar och konfigurerar en lokal datagateway.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bd4c024e14e70b5937d85e9917340d25f552096d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58110876"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Installera och konfigurera en lokal datagateway

En lokal datagateway krävs när en eller flera Azure Analysis Services-servrar i samma region som ansluter till lokala datakällor. Läs mer om gatewayen i [lokal datagateway](analysis-services-gateway.md).

## <a name="prerequisites"></a>Förutsättningar

**Minimikrav:**

* .NET 4.5 Framework
* 64-bitars version av Windows 7 / Windows Server 2008 R2 (eller senare)

**Rekommenderat:**

* Processor med 8 kärnor
* 8 GB minne
* 64-bitars version av Windows 2012 R2 (eller senare)

**Att tänka på:**

* Under installationen när du registrerar din gateway med Azure, väljs standardregionen för din prenumeration. Du kan välja en annan region. Om du har servrar i flera regioner måste du installera en gateway för varje region. 
* Gatewayen kan inte installeras på en domänkontrollant.
* Endast en gateway kan installeras på en dator.
* Installera gatewayen på en dator som finns kvar och går inte i viloläge.
* Installera inte gatewayen på en dator som trådlöst anslutna till nätverket. Prestanda kan minskas.
* När du installerar gatewayen, måste du är inloggad på datorn med användarkontot ha Log på som tjänsten privilegier. När installationen är klar använder lokala data gateway-tjänsten NT SERVICE\PBIEgwService-konto för att logga in som en tjänst. Ett annat konto kan anges under installationen eller i tjänster när installationen är klar. Kontrollera inställningarna för grupprincipen Tillåt både det konto som du är inloggad med när du installerar och det tjänstkonto som du väljer ha loggen som tjänsten privilegier.
* Logga in på Azure med ett konto i Azure AD för samma [klient](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) som prenumerationen som du registrerar gatewayen på den. Azure B2B (Gäst)-konton stöds inte när du installerar och registrerar en gateway.
* Om datakällor finns på Azure Virtual Network (VNet), måste du konfigurera den [AlwaysUseGateway](analysis-services-vnet-gateway.md) serveregenskap.
* (Enhetlig) gatewayen beskrivs här stöds inte i Azure Tyskland-regioner. Använd i stället **dedikerad lokal gateway för Azure Analysis Services**, installerad från serverns **Snabbstart** i portalen. 


## <a name="download"></a>Ladda ned

 [Ladda ned gatewayen](https://aka.ms/azureasgateway)

## <a name="install"></a>Installera

1. Kör installationsprogrammet.

2. Välj en plats, godkänner licensvillkoren och klicka sedan på **installera**.

   ![Installera platsen och licensvillkoren](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Logga in i Azure. Kontot måste vara i din klients Azure Active Directory. Det här kontot används för gateway-administratören. Azure B2B (Gäst)-konton stöds inte när du installerar och registrera Gateway-servern.

   ![Logga in på Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Om du loggar in med ett domänkonto, är det kopplat till ditt organisationskonto i Azure AD. Ditt organisationskonto används som gateway-administratören.

## <a name="register"></a>Registrera dig

Du måste registrera den lokala instansen som du installerade med Gateway-Molntjänsten för att skapa en resurs för gatewayen i Azure. 

1.  Välj **registrera en ny gateway på den här datorn**.

    ![Registrera dig](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Ange ett namn och återställning nycklarna för din gateway. Som standard använder gatewayen standardregionen för din prenumeration. Om du måste välja en annan region, väljer **ändra Region**.

    > [!IMPORTANT]
    > Spara återställningsnyckeln på en säker plats. Återställningsnyckeln är nödvändiga för att ta över, migrera eller återställa en gateway. 

   ![Registrera dig](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Skapa en Azure-gateway-resurs

När du har installerat och registrerat din gateway, måste du skapa en resurs för gatewayen i Azure-prenumerationen. Logga in på Azure med samma konto som du använde när du registrera Gateway-servern.

1. I Azure-portalen klickar du på **skapa en resurs** > **integrering** > **lokal datagateway**.

   ![Skapa en resurs för gatewayen](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. I **skapa anslutningsgatewayen**, anger dessa inställningar:

   * **Namn**: Ange ett namn för din gatewayresursen. 

   * **Prenumeration**: Välj den Azure-prenumerationen ska associeras med din gateway-resurs. 
   
     Standard-prenumerationen baseras på Azure-konto som du använde för att logga in.

   * **Resursgrupp**: Skapa en resursgrupp eller välj en befintlig resursgrupp.

   * **Plats**: Välj den region som du har registrerat din gateway på.

   * **Installationsnamn**: Om din gateway-installation inte redan är markerat, Välj den gateway som registrerats. 

     När du är klar klickar du på **skapa**.

## <a name="connect-servers"></a>Anslut servrar till gateway-resurs

1. I din Azure Analysis Services översikt över server, klickar du på **On-Premises Data Gateway**.

   ![Ansluta servern till gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. I **Välj en lokal Datagateway att ansluta**, Välj din gateway-resurs och klicka sedan på **Anslut markerad gateway**.

   ![Anslut servern till gateway-resurs](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Om din gateway inte visas i listan, är din server förmodligen inte i samma region som regionen som du angav när du registrerar gatewayen. 

Klart! Om du behöver öppna portar eller göra en felsökning kan du kolla in [lokal datagateway](analysis-services-gateway.md).

## <a name="next-steps"></a>Nästa steg

* [Manage Analysis Services](analysis-services-manage.md)   
* [Hämta data från Azure Analysis Services](analysis-services-connect.md)   
* [Använda gateway för datakällor på ett virtuellt Azure-nätverk](analysis-services-vnet-gateway.md)
