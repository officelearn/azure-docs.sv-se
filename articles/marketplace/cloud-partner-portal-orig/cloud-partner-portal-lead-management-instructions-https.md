---
title: HTTPS-slutpunkt | Microsoft Docs
description: Konfigurera lead-hantering för Https.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: fd13a7281c7e8702fd199364261ebcd458db0555
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811266"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurera lead-hantering med hjälp av en HTTPS-slutpunkt

Du kan använda en HTTPS-slutpunkt för att hantera Azure Marketplace och AppSource leads som kan skrivas till ett CRM-system. Den här artikeln beskriver hur du konfigurerar lead-hantering med Microsoft Flow automation-tjänsten.


## <a name="create-a-flow-using-microsoft-flow"></a>Skapa ett flöde som använder Microsoft Flow

1.  Öppna den [Flow](https://flow.microsoft.com/) webbsidan. Välj **logga in** eller välj **registrera dig kostnadsfritt** att skapa en kostnadsfri Flow-konto.

2.  Logga in och välj **Mina flöden** på menyraden.

    ![Mina flöden](./media/cloud-partner-portal-lead-management-instructions-https/image001.png)

3.  Välj **skapa från tom**.

    ![Skapa från början](./media/cloud-partner-portal-lead-management-instructions-https/image003.png)


4.  Välj den **begäran/svar** anslutningstjänsten och sök sedan efter begäranutlösaren. 

    ![Skapa från början](./media/cloud-partner-portal-lead-management-instructions-https/image005.png)

5. Välj den **begära** utlösaren.
    ![Begäran-utlösare](./media/cloud-partner-portal-lead-management-instructions-https/image007.png)


6.  Kopiera den **exempel JSON** i slutet av den här artikeln till den **begär JSON-Brödtextsschema**.

7.  Lägg till ett nytt steg och välj CRM-systemet för med åtgärden att skapa en ny post. Nästa skärmdumpar visar **Dynamics 365 – skapa en ny post** som exempel.

    ![Skapa en ny post](./media/cloud-partner-portal-lead-management-instructions-https/image009.png)

8.  Ange anslutningsindata som för din anslutning och välj den **leder** entitet.

    ![Välj leads](./media/cloud-partner-portal-lead-management-instructions-https/image011.png)

9.  Flöden visar ett formulär för att tillhandahålla lead-information. Du kan mappa objekt från den inkommande begäran genom att välja att lägga till dynamiskt innehåll.

    ![Lägg till dynamiskt innehåll](./media/cloud-partner-portal-lead-management-instructions-https/image013.png)

10.  Mappa fälten du vill och välj sedan **spara** att spara ditt flöde.

11. En HTTP POST-Webbadress har skapats i begäran. Kopiera URL: en och använda den som HTTPS-slutpunkten.

    ![HTTP post-URL](./media/cloud-partner-portal-lead-management-instructions-https/image015.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurera ditt erbjudande till skicka leads till HTTPS-slutpunkt

När du konfigurerar lead hanteringsinformation för ditt erbjudande, Välj **HTTPS-slutpunkt** för leda mål och klistra in URL: en för HTTP POST som du kopierade i föregående steg.  

![Lägg till dynamiskt innehåll](./media/cloud-partner-portal-lead-management-instructions-https/image017.png)

När leads genereras skickar Microsoft leads till flödet som dirigeras till CRM-systemet som du har konfigurerat.


## <a name="json-example"></a>JSON-exempel

``` json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "definitions": {},
  "id": "http://example.com/example.json",
  "properties": {
    "ActionCode": {
      "id": "/properties/ActionCode",
      "type": "string"
    },
    "OfferTitle": {
      "id": "/properties/OfferTitle",
      "type": "string"
    },
    "LeadSource": {
      "id": "/properties/LeadSource",
      "type": "string"
    },
    "UserDetails": {
      "id": "/properties/UserDetails",
      "properties": {
        "Company": {
          "id": "/properties/UserDetails/properties/Company",
          "type": "string"
        },
        "Country": {
          "id": "/properties/UserDetails/properties/Country",
          "type": "string"
        },
        "Email": {
          "id": "/properties/UserDetails/properties/Email",
          "type": "string"
        },
        "FirstName": {
          "id": "/properties/UserDetails/properties/FirstName",
          "type": "string"
        },
        "LastName": {
          "id": "/properties/UserDetails/properties/LastName",
          "type": "string"
        },
        "Phone": {
          "id": "/properties/UserDetails/properties/Phone",
          "type": "string"
        },
        "Title": {
          "id": "/properties/UserDetails/properties/Title",
          "type": "string"
        }
      },
      "type": "object"
    }
  },
  "type": "object"
}
```
