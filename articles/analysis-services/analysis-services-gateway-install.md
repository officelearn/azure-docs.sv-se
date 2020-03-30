---
title: Installera lokal datagateway för Azure Analysis Services | Microsoft-dokument
description: Lär dig hur du installerar och konfigurerar en lokal datagateway för att ansluta till lokala datakällor från en Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f6218b32fb9574adf62384d2a6ee5a62f3788de8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062157"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Installera och konfigurera lokal datagateway

En lokal datagateway krävs när en eller flera Azure Analysis Services-servrar i samma region ansluter till lokala datakällor.  Även om gatewayen du installerar är densamma som används av andra tjänster som Power BI, Power Apps och Logic Apps, när du installerar för Azure Analysis Services, finns det några ytterligare steg som du måste slutföra. Den här installationsartikeln är specifik för **Azure Analysis Services**. 

Mer information om hur Azure Analysis Services fungerar med gatewayen finns i [Ansluta till lokala datakällor](analysis-services-gateway.md). Mer information om avancerade installationsscenarier och gatewayen i allmänhet finns i [Dokumentation för lokala datagateways](/data-integration/gateway/service-gateway-onprem).

## <a name="prerequisites"></a>Krav

**Minimikrav:**

* .NET 4.5 Framework
* 64-bitarsversion av Windows 8 / Windows Server 2012 R2 (eller senare)

**Rekommenderas:**

* 8 CPU-kärnor
* 8 GB minne
* 64-bitarsversion av Windows 8 / Windows Server 2012 R2 (eller senare)

**Viktiga överväganden:**

* Under installationen, när du registrerar din gateway med Azure, väljs standardregionen för din prenumeration. Du kan välja en annan prenumeration och region. Om du har servrar i mer än en region måste du installera en gateway för varje region. 
* Gatewayen kan inte installeras på en domänkontrollant.
* Endast en gateway kan installeras på en enda dator.
* Installera gatewayen på en dator som är på och som förs i viloläge.
* Installera inte gatewayen på en dator med en trådlös enda anslutning till nätverket. Prestanda kan minskas.
* När du installerar gatewayen måste användarkontot som du är inloggad på datorn med ha inloggning som tjänstbehörighet. När installationen är klar använder den lokala datagatewaytjänsten kontot NT SERVICE\PBIEgwService för att logga in som en tjänst. Ett annat konto kan anges under installationen eller i Tjänster när installationen är klar. Se till att grupprincipinställningarna tillåter både det konto du är inloggad med när du installerar och det tjänstkonto du väljer har inloggning som tjänstbehörighet.
* Logga in på Azure med ett konto i Azure AD för samma [klient som](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) prenumerationen som du registrerar gatewayen i. Azure B2B-konton (gäst) stöds inte när du installerar och registrerar en gateway.
* Om datakällor finns på ett Virtuellt Azure-nätverk (VNet) måste du konfigurera egenskapen [AlwaysUseGateway server.](analysis-services-vnet-gateway.md)

## <a name="download"></a><a name="download"></a>Hämta

 [Ladda ner gatewayen](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a><a name="install"></a>Installera

1. Kör installationen.

2. Välj **Lokal datagateway**.

   ![Välj](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Välj en plats, acceptera villkoren och klicka sedan på **Installera**.

   ![Installera plats- och licensvillkor](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Logga in i Azure. Kontot måste finnas i din klients Azure Active Directory. Det här kontot används för gateway-administratören. Azure B2B-konton (gäst) stöds inte när gatewayen installeras och registreras.

   ![Logga in på Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Om du loggar in med ett domänkonto mappas det till ditt organisationskonto i Azure AD. Ditt organisationskonto används som gatewayadministratör.

## <a name="register"></a><a name="register"></a>Registrera

För att skapa en gateway-resurs i Azure måste du registrera den lokala instansen som du installerade med Gateway Cloud Service. 

1.  Välj **Registrera en ny gateway på den här datorn**.

    ![Registrera dig](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Skriv ett namn och en återställningsnyckel för gatewayen. Som standard använder gatewayen prenumerationens standardregion. Om du behöver välja en annan region väljer du **Ändra region**.

    > [!IMPORTANT]
    > Spara återställningsnyckeln på ett säkert ställe. Återställningsnyckeln krävs för att ta över, migrera eller återställa en gateway. 

   ![Registrera dig](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a><a name="create-resource"></a>Skapa en Azure gateway-resurs

När du har installerat och registrerat din gateway måste du skapa en gateway-resurs i Azure. Logga in på Azure med samma konto som du använde när du registrerade gatewayen.

1. I Azure-portalen klickar du på **Skapa en resurs**och söker sedan efter Lokal **datagateway**och klickar sedan på **Skapa**.

   ![Skapa en gatewayresurs](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Ange följande inställningar i **Skapa anslutningsgateway:**

   * **Namn**: Ange ett namn på gatewayresursen. 

   * **Prenumeration:** Välj Azure-prenumerationen som ska associeras med gateway-resursen. 
   
     Standardprenumerationen baseras på det Azure-konto som du använde för att logga in.

   * **Resursgrupp**: Skapa en resursgrupp eller välj en befintlig resursgrupp.

   * **Plats**: Välj den region som du registrerade din gateway i.

   * **Installationsnamn:** Om gatewayinstallationen inte redan är markerad väljer du den gateway som du installerade på datorn och registrerade. 

     När du är klar klickar du på **Skapa**.

## <a name="connect-servers-to-the-gateway-resource"></a><a name="connect-servers"></a>Ansluta servrar till gatewayresursen

1. Klicka på **Lokal datagateway**i serveröversikten för Azure Analysis Services .

   ![Ansluta server till gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Välj **gatewayresurs i Välj en lokal datagateway för att ansluta**och klicka sedan på Anslut vald **gateway**.

   ![Ansluta server till gatewayresurs](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Om gatewayen inte visas i listan är servern troligen inte i samma region som den region som du angav när gatewayen registrerades.

    När anslutningen mellan servern och gatewayresursen lyckas visas **Ansluten**status .


    ![Ansluta server till gateway resurs framgång](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

Klart! Om du behöver öppna portar eller göra felsökning måste du checka ut [Lokal datagateway](analysis-services-gateway.md).

## <a name="next-steps"></a>Nästa steg

* [Manage Analysis Services](analysis-services-manage.md)   
* [Hämta data från Azure Analysis Services](analysis-services-connect.md)   
* [Använda gateway för datakällor på ett virtuellt Azure-nätverk](analysis-services-vnet-gateway.md)
