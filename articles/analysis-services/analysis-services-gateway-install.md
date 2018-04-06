---
title: Installera lokala datagateway | Microsoft Docs
description: Lär dig hur du installerar och konfigurerar en lokala datagateway.
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
tags: ''
ms.assetid: ''
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/03/2018
ms.author: owend
ms.openlocfilehash: b57ee7d9617f8bf2e54122ac37f351ae0cf9db26
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Installera och konfigurera en gateway för lokala data
En lokal datagateway krävs när en eller flera Azure Analysis Services-servrar i samma region som ansluter till lokala datakällor. Mer information om gateway finns [lokala datagateway](analysis-services-gateway.md).

## <a name="prerequisites"></a>Krav
**Minimikrav:**

* .NET 4.5 Framework
* 64-bitars version av Windows 7 / Windows Server 2008 R2 (eller senare)

**Rekommenderat:**

* 8 kärnor CPU
* 8 GB minne
* 64-bitarsversionen av Windows 2012 R2 (eller senare)

**Viktigt att tänka på:**

* Under installationen när du registrerar din gateway med Azure, har standardregionen för din prenumeration valts. Du kan välja en annan region. Om du har servrar i flera regioner, måste du installera en gateway för varje region. 
* Gatewayen kan inte installeras på en domänkontrollant.
* Endast en gateway kan installeras på en dator.
* Installera gatewayen på en dator som finns kvar på och går inte för att viloläge.
* Installera inte gatewayen på en dator som trådlöst ansluten till nätverket. Prestanda kan minskas.
* Logga in på Azure med ett konto i Azure AD för samma [klient](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) som prenumerationen du registrerar gatewayen på den. Azure B2B (gästkonton) stöds inte när du installerar och registrerar en gateway.
* (Enhetlig) gatewayen beskrivs här stöds inte i Azure Government (Fairfax). Använd den dedicerade lokala gatewayen för Azure Analysis Services. Dedikerad gateway stöder inte ytterligare tjänster som Power BI och Power appar. Dedikerad gateway har installerats från dina servrar Snabbstart i portalen.


## <a name="download"></a>Ladda ned
 [Ladda ned gatewayen](https://aka.ms/azureasgateway)

## <a name="install"></a>Installera

1. Kör installationsprogrammet.

2. Välj en plats, accepterar villkoren och klicka sedan på **installera**.

   ![Installera platsen och licensvillkoren](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Logga in i Azure. Kontot måste vara i din klient Azure Active Directory. Det här kontot används för gateway-administratören. Azure B2B (gästkonton) stöds inte när installera och registrera gatewayen.

   ![Logga in till Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Om du loggar in med ett domänkonto är mappad till ditt organisationskonto i Azure AD. Ditt organisationskonto används som gateway-administratören.

## <a name="register"></a>Registrera dig
För att skapa en gateway-resurs i Azure måste du registrera den lokala instansen av du installerat med Gateway-Molntjänsten. 

1.  Välj **registrera en ny gateway på den här datorn**.

    ![Registrera dig](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Skriv ett namn och återställa nyckeln för din gateway. Som standard används gatewayen som standardregion för din prenumeration. Om du måste välja en annan region, väljer **ändra Region**.

    > [!IMPORTANT]
    > Spara återställningsnyckeln på en säker plats. Återställningsnyckeln är nödvändiga i ordning till övertag, migrera eller återställa en gateway. 

   ![Registrera dig](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Skapa en Azure gateway-resurs
När du har installerat och registrerat din gateway, måste du skapa en gateway-resurs i din Azure-prenumeration. Logga in på Azure med samma konto som du använde när du registrerar en gateway.

1. I Azure-portalen klickar du på **skapa en ny tjänst** > **Enterprise Integration** > **lokala datagateway**  >   **Skapa**.

   ![Skapa en gateway-resurs](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. I **skapa anslutningen gateway**, ange dessa inställningar:

    * **Namnet**: Ange ett namn för din gateway-resurs. 

    * **Prenumerationen**: Välj den Azure-prenumerationen ska associeras med din gateway-resurs. 
   
      Standard-prenumerationen är baserad på det Azure-konto som du använde för att logga in.

    * **Resursgrupp**: Skapa en resursgrupp eller välj en befintlig resursgrupp.

    * **Plats**: Välj den region som du har registrerat din gateway i.

    * **Installationen namnet**: om gatewayinstallationen av inte redan har markerats väljer du den gateway som har registrerats. 

    När du är klar klickar du på **skapa**.

## <a name="connect-servers"></a>Anslut servrar till gateway-resurs

1. I din Azure Analysis Services översikt över server, klickar du på **lokala Data Gateway**.

   ![Anslut servern till gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. I **Välj en lokala Data Gateway för att ansluta**, väljer du gateway-resurs och klicka sedan på **Anslut markerad gateway**.

   ![Anslut servern till gateway-resurs](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Om din gateway inte visas i listan, sannolikt servern inte i samma region som regionen som du angav när du registrerar en gateway. 

Det var allt. Om du behöver öppna portar eller göra felsökning bör du kolla [lokala datagateway](analysis-services-gateway.md).

## <a name="next-steps"></a>Nästa steg
* [Hantera Analysis Services](analysis-services-manage.md)   
* [Hämta data från Azure Analysis Services](analysis-services-connect.md)
